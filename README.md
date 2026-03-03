# Examples Clean Architecture

![Build Status](https://img.shields.io/badge/build-passing-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue)
![Version](https://img.shields.io/badge/version-1.0.0-orange)

## Project Overview
This repository contains various examples of Clean Architecture implementations, showcasing the principles of Clean Architecture, CQRS (Command Query Responsibility Segregation), and DDD (Domain Driven Design). These examples aim to demonstrate how to build maintainable, testable, and scalable applications.

## Setup Instructions
1. Clone the repository:
   ```bash
   git clone https://github.com/gonzaromeroatton/examples-clean-arquitecture.git
   cd examples-clean-arquitecture
   ```
2. Install dependencies:
   ```bash
   npm install
   ```

## Run/Test Steps
To run the application, use:
```bash
npm start
```
To run the tests, use:
```bash
npm test
```

## Folder Structure
```
/examples
  ├── service-a
  │   ├── src
  │   └── tests
  ├── service-b
  │   ├── src
  │   └── tests
  └── shared
      ├── domain
      ├── infrastructure
      └── application
```

## Architecture Explanation
### Clean Architecture
Clean Architecture is a way of structuring applications to make them independent of frameworks, UI, and databases. The aim is to create a separation of concerns, allowing for greater flexibility and easier testing.

```mermaid
graph TD;
    A[Entities] --> B[Use Cases];
    B --> C[Interface Adapters];
    C --> D[Frameworks & Drivers];
    A --> D;
```

### CQRS
CQRS separates the read and write sides of an application, allowing for different models to handle each side. This separation can help optimize performance, scalability, and security.

```mermaid
graph TD;
    A[Command] --> B[Command Handler];
    B --> C[Aggregate];
    C --> D[Event];
    D --> E[Event Store];
    F[Query] --> G[Query Handler];
    G --> H[Read Model];
```

### DDD
Domain Driven Design focuses on creating a shared understanding of the problem domain between teams and encapsulating logic into domain models. This fosters better communication and a more organized codebase.

```mermaid
graph TD;
    A[Domain] --> B[Entities];
    A --> C[Value Objects];
    A --> D[Aggregates];
    A --> E[Repositories];
    A --> F[Services];
```

## Contribution
Contributions are welcome! Please check the [contributing guidelines](CONTRIBUTING.md) for details.

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.