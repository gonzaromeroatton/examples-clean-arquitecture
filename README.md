# Examples.CleanArquitecture — Clean REST API (CQRS + DDD)

![.NET](https://img.shields.io/badge/.NET-9.0-512BD4?logo=dotnet&logoColor=white)
![C%23](https://img.shields.io/badge/C%23-100%25-239120?logo=csharp&logoColor=white)
![ASP.NET%20Core](https://img.shields.io/badge/ASP.NET%20Core-Web%20API-5C2D91?logo=dotnet&logoColor=white)
![CQRS](https://img.shields.io/badge/Pattern-CQRS-orange)
![DDD](https://img.shields.io/badge/Design-DDD-blue)
![Clean%20Architecture](https://img.shields.io/badge/Architecture-Clean%20Architecture-success)
![Swagger](https://img.shields.io/badge/OpenAPI-Swagger-85EA2D?logo=swagger&logoColor=black)
![EF%20Core](https://img.shields.io/badge/ORM-Entity%20Framework%20Core-512BD4)
![SQL%20Server](https://img.shields.io/badge/DB-SQL%20Server-CC2927?logo=microsoftsqlserver&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-blue)

Clean API REST architecture example using **Clean Architecture**, **CQRS** and **DDD**.  
The **main entry point** is the **API** project (`Examples.CleanArquitecture.API`).

---

## Table of contents

- [Repository description](#repository-description)
- [Architecture at a glance](#architecture-at-a-glance)
- [Solution structure](#solution-structure)
- [Tech stack](#tech-stack)
- [Getting started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Run the API](#run-the-api)
  - [OpenAPI / Swagger](#openapi--swagger)
- [Configuration](#configuration)
  - [Database](#database)
  - [Email](#email)
- [API endpoints (example)](#api-endpoints-example)
- [CQRS flow](#cqrs-flow)
- [Diagrams](#diagrams)
- [License](#license)

---

## Repository description

> **Clean API REST arquitecture example, CQRS and DDD.**

This repo demonstrates a common setup for backend systems where:
- The **Domain** contains the core business model
- The **Application** layer contains **use cases** (CQRS handlers)
- The **Infrastructure / Persistence** layers integrate external concerns (email, database, etc.)
- The **API** exposes HTTP endpoints and delegates work through **MediatR**

---

## Architecture at a glance

**Rule of thumb:** dependencies flow inwards.

- **API** → references Application, Infraestructure, Persistence  
- **Infraestructure** → references Application  
- **Persistence** → references Application  
- **Application** → references Domain  
- **Domain** → references nothing (ideally)

---

## Solution structure

Solution file:

- `Examples.CleanArquitecture.sln`

Projects (under `Examples.CleanArquitecture/`):

- `Examples.CleanArquitecture.API` (main project)
- `Examples.CleanArquitecture.Application`
- `Examples.CleanArquitecture.Domain`
- `Examples.CleanArquitecture.Infraestructure`
- `Examples.CleanArquitecture.Persistence`

---

## Tech stack

- **.NET 9** (`net9.0`)
- **ASP.NET Core Web API**
- **MediatR** (CQRS dispatch)
- **AutoMapper**
- **FluentValidation**
- **Entity Framework Core** + **SQL Server provider**
- **Swagger / OpenAPI** (Swashbuckle)
- **SendGrid** (email provider)

---

## Getting started

### Prerequisites

- Install **.NET SDK 9**
- If you want to use the default connection string, you can use **SQL Server LocalDB** (Windows).  
  Otherwise, update the connection string to point to your SQL Server instance.

### Run the API

From the repository root:

```bash
dotnet restore
dotnet build
dotnet run --project Examples.CleanArquitecture/Examples.CleanArquitecture.API/Examples.CleanArquitecture.API.csproj
```

The API is configured with:
- Controllers
- CORS policy named `all` (AllowAnyOrigin/Method/Header)
- HTTPS redirection
- Swagger enabled in Development
- Custom exception middleware

### OpenAPI / Swagger

When running in `Development`, Swagger UI is enabled. Open:

- `https://localhost:<port>/swagger`

---

## Configuration

### Database

`Examples.CleanArquitecture.API/appsettings.json` contains:

- Connection string key: `ConnectionStrings:PersonsConnectionString`
- Default value:
  - `Server=(localdb)\mssqllocaldb;Database=db_persons;Trusted_Connection=True;MultipleActiveResultSets=true`

If you are not using LocalDB, change it to your SQL Server connection string.

### Email

`Examples.CleanArquitecture.API/appsettings.json` contains:

```json
"EmailSettings": {
  "ApiKey": "SendGrid-Key",
  "FromAddress": "no-reply@persons.com",
  "FromName": "Persons Management System"
}
```

Set your real SendGrid API key and sender information as needed.

---

## API endpoints (example)

The sample domain shown in the API layer is **Person**.

Controller:
- `api/person`

Available example endpoints:

- `GET /api/person/{id}`
- `GET /api/person/dni/{dni}`

These endpoints call the Application layer through **MediatR** queries:
- `GetPersonByIdQuery`
- `GetPersonByDniQuery`

---

## CQRS flow

This project uses CQRS via MediatR:

- **API Controller** receives HTTP request
- Sends a **Command** or **Query** via `IMediator`
- **Handler** executes the use case in the Application layer
- Handler interacts with:
  - Domain model (business rules)
  - Persistence (EF Core / DbContext, repositories, etc.)
  - Infraestructure services (e.g., email via SendGrid)

---

## Diagrams

### Clean Architecture (project dependencies)

```mermaid
flowchart LR
  API["Examples.CleanArquitecture.API<br/>(ASP.NET Core Web API)"]
  APP["Examples.CleanArquitecture.Application<br/>(CQRS: Commands/Queries + Handlers)"]
  DOM["Examples.CleanArquitecture.Domain<br/>(Entities, Value Objects, Domain Rules)"]
  INFRA["Examples.CleanArquitecture.Infraestructure<br/>(External services: Email, etc.)"]
  PERS["Examples.CleanArquitecture.Persistence<br/>(EF Core, SQL Server)"]

  API --> APP
  API --> INFRA
  API --> PERS

  INFRA --> APP
  PERS --> APP

  APP --> DOM
```

### Request flow (HTTP → MediatR → Use Case)

```mermaid
sequenceDiagram
  participant Client
  participant API as API Controller
  participant MediatR
  participant Handler as Query/Command Handler (Application)
  participant Persistence as Persistence (EF Core)
  participant Infra as Infraestructure (e.g. Email)

  Client->>API: HTTP Request (e.g. GET /api/person/{id})
  API->>MediatR: Send(Query)
  MediatR->>Handler: Dispatch to handler
  Handler->>Persistence: Load/Save data
  Handler->>Infra: Optional side effects (email, etc.)
  Handler-->>MediatR: Result DTO
  MediatR-->>API: Result
  API-->>Client: HTTP Response (200/4xx/5xx)
```

### CQRS (Commands vs Queries)

```mermaid
flowchart TB
  subgraph WriteSide["Write Side (Commands)"]
    C["Command"] --> CH["Command Handler"]
    CH --> D1["Domain Model"]
    CH --> P1["Persistence (EF Core)"]
  end

  subgraph ReadSide["Read Side (Queries)"]
    Q["Query"] --> QH["Query Handler"]
    QH --> P2["Persistence (EF Core)"]
    QH --> DTO["DTO / ViewModel"]
  end

  API["API"] --> C
  API --> Q
```

---

## License

This project is licensed under the **MIT License**. See [LICENSE](LICENSE).
