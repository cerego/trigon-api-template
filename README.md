
# TypeScript Project Structure and Testing Guide for REST API Development

This document serves as a reference manual for the dev team, outlining best practices for structuring a new TypeScript-based REST API project. The goal is to maintain clarity, scalability, and easy maintainability. We adopt a modular structure with unit tests placed close to the source files and a parallel `tests` folder for integration and end-to-end testing.

---

## Project Structure Overview

Our project is structured to keep each component modular and purpose-driven. All core API code resides in the `src` folder, with a parallel `tests` folder for integration and end-to-end tests.

### Folder Structure

```plaintext
project-root
├── src                     # Core application code
│   ├── api
│   │   ├── config          # Configuration files (database, environment, etc.)
│   │   ├── controllers     # Handle request and response logic
│   │   ├── domain          # Data models and types
│   │   │   ├── schemas     # Schema definitions (e.g., for ORMs)
│   │   │   └── types       # TypeScript interfaces for data models
│   │   ├── helpers         # Utility functions and reusable code
│   │   ├── interfaces      # Infrastructure interfaces (e.g., Database, FileRepository)
│   │   ├── adapters        # Implementations of infrastructure interfaces
│   │   ├── middlewares     # Middleware for request handling
│   │   ├── routes          # Route definitions for API endpoints
│   │   ├── services        # Business logic connecting controllers and models
│   │   └── validations     # Request validation logic
├── tests                   # Integration, end-to-end, and system tests
│   ├── integration         # Integration tests for interactions between components
│   ├── e2e                 # End-to-end tests for simulating real-world scenarios
│   └── mocks               # Shared mocks for integration and end-to-end tests
├── package.json
├── tsconfig.json
└── jest.config.js          # Jest config file for managing different test types
```

---

## Detailed Breakdown

Explanation and purpose of each folder in the project structure, with examples.

### `config/`
- **Purpose**: Stores configuration files for application setup (e.g., database connections, environment settings).
- **Example**: `database.ts` might export a function that sets up and returns a database connection instance based on environment variables.

### `controllers/`
- **Purpose**: Handle incoming requests, process them, and return appropriate responses. Controllers are thin, focusing on directing traffic to services and sending responses.
- **Example**: `user.controller.ts` contains methods like `getUser`, `createUser`, etc., which process HTTP requests and invoke service methods accordingly.
- **Test Folder**: Each controller has a `tests` subfolder for unit tests verifying controller methods.

### `domain/`
Contains data models and types, organized in subfolders:

- **Schemas** (`schemas/`): Defines schemas for ORM or database modeling, specifying data structure, validation, and relationships.
  - **Example**: `User.schema.ts` defines the fields, types, and constraints for the user model in the database.
- **Types** (`types/`): Holds TypeScript interfaces for data models, ensuring type safety across the application.
  - **Example**: `User.type.ts` defines `User` interface, describing the structure of user data used in services.

### `helpers/`
- **Purpose**: Reusable utility functions used across the application, not specific to business logic.
- **Example**: `formatDate.ts` could contain a utility function for formatting dates consistently.

### `interfaces/`
- **Purpose**: Define infrastructure interfaces for common functionality, promoting loose coupling and reusability.
- **Example**: `Database.ts` defines methods for database operations (e.g., `connect`, `disconnect`). Implementations can use this interface to ensure consistent interactions.

### `adapters/`
- **Purpose**: Implementations of interfaces, adapting the application to specific technologies (e.g., DynamoDB, S3).
- **Example**: `DynamoDBAdapter.ts` in `adapters/database` provides the implementation for `Database` interface using DynamoDB, while `S3Adapter.ts` implements file storage functionality.

### `middlewares/`
- **Purpose**: Custom middleware functions to handle cross-cutting concerns in request handling.
- **Example**: `auth.middleware.ts` could validate JWT tokens to authenticate requests.

### `routes/`
- **Purpose**: Define API endpoint paths and map them to controller methods, organizing all routes by feature or resource.
- **Example**: `user.routes.ts` defines all routes related to user operations, such as `/users`, `/users/:id`.

### `services/`
- **Purpose**: Business logic and operations, handling tasks that require data manipulation and processing before reaching the controllers.
- **Example**: `UserService.ts` might contain `getUserById` or `createUser` methods, orchestrating logic and calling database adapters.

### `validations/`
- **Purpose**: Define request validation schemas for API requests to ensure data integrity and consistency.
- **Example**: `user.validation.ts` could use a library like Joi to validate fields in user creation requests.

---

## Testing Structure

### In-Source Unit Tests

Each relevant folder within `src/api/` includes a `tests` subfolder for unit tests. The parallel `tests` folder is dedicated to integration and end-to-end tests.

### Parallel `tests` Folder

The `tests` folder, parallel to `src`, is dedicated to higher-level testing, including integration and end-to-end tests.

### Example Structure

```plaintext
tests/
├── integration         # Integration tests for combined components (e.g., controllers + services)
├── e2e                 # End-to-end tests for full API workflows
└── mocks               # Shared mocks for integration and E2E tests
```

### Test Types

#### Unit Tests
Validates isolated functionality for each component in `src/api`. Placed within `tests` folders in each subfolder.

#### Integration Tests
Tests how multiple components interact (e.g., controller-to-service communication).

#### End-to-End (E2E) Tests
Validates end-to-end functionality across routes and services, simulating real-world use cases.

#### Mocks
Centralize mock data and setup for integration and E2E tests.

---

## Key Best Practices

- Organize code by functionality, keeping each component isolated in purpose.
- Use interfaces in `interfaces` and implement them in `adapters` for flexibility and testability.
- Centralize mocks in `tests/mocks` for consistency across tests.
- Separate unit tests within `src` from integration and E2E tests in the parallel `tests` folder.

---

## Summary

This structure provides a scalable, maintainable approach to building and testing a TypeScript REST API. By separating unit tests from integration and end-to-end tests, this approach ensures clear organization, test isolation, and flexibility as the project grows.
