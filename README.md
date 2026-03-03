# Examples.CleanArquitecture

Clean REST API example applying **Clean Architecture**, **DDD-style domain modeling**, and **CQRS** with **MediatR**.  
The sample exposes a `Person` resource and demonstrates layering, dependency injection, centralized exception handling, persistence with **Entity Framework Core**, and CI with **GitHub Actions**.

> Note: the solution/project name uses `Arquitecture` (typo kept intentionally because it matches the repository/solution naming).

---

## Contents

- [Architecture overview](#architecture-overview)
- [Solution structure](#solution-structure)
- [Tech stack](#tech-stack)
- [Getting started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Configuration](#configuration)
  - [Run the API](#run-the-api)
  - [Database & migrations](#database--migrations)
- [API usage](#api-usage)
- [Error handling](#error-handling)
- [Cross-Origin Resource Sharing (CORS)](#cross-origin-resource-sharing-cors)
- [CI (GitHub Actions)](#ci-github-actions)
- [License](#license)

---

## Architecture overview

This repository follows the typical Clean Architecture direction of dependencies:

- **API** depends on **Application** (CQRS handlers / use cases)
- **Application** depends on **Domain** (entities)
- **Infrastructure** and **Persistence** implement interfaces defined in **Application**
- **Domain** is the center and has no dependency on outer layers

Main patterns and ideas used:

- **CQRS**: reads are implemented as queries (e.g. `GetPersonByIdQuery`, `GetPersonByDniQuery`) handled via **MediatR**.
- **DDD-style domain**: entities live in the `Domain` project (e.g. `Person : BaseEntity`).
- **DI composition root**: dependency registration is centralized via `AddApplicationServices`, `AddInfraestructureServices`, and `AddPersistenceServices`.

---

## Solution structure

Solution file:

- `Examples.CleanArquitecture.sln`

Projects (under `Examples.CleanArquitecture/`):

- `Examples.CleanArquitecture.API`  
  ASP.NET Core Web API project. Hosts controllers, middleware, Swagger, and the DI composition root (`Program.cs`).

- `Examples.CleanArquitecture.Application`  
  Application layer (CQRS queries/handlers, DTOs, contracts, mapping, exceptions, etc.). Contains `ApplicationServiceRegistration.cs`.

- `Examples.CleanArquitecture.Domain`  
  Core domain entities. Example: `Person`.

- `Examples.CleanArquitecture.Persistence`  
  EF Core context, migrations, and repository implementations. Contains `PersonsDatabaseContext` and `PersistenceServiceRegistration.cs`.

- `Examples`

