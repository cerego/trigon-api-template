
# TypeScript Monorepo Project Structure and Testing Guide for REST API Development with Fastify

This document provides a reference for setting up a TypeScript-based monorepo with packages for core API functionality (`core`) and additional functions (`functions`). This monorepo structure promotes code reuse, modularity, and easy maintainability. It includes Fastify as the web framework for handling incoming requests.

---

## Monorepo Structure Overview

In this monorepo, packages are organized under `packages/`, where each package contains its own `src` folder and dependencies managed at the root level. A `tests` folder at the root holds integration and end-to-end tests.

### Folder Structure

```plaintext
project-root
├── packages
│   ├── core                     # Core API application code
│   │   ├── src
│   │   │   ├── config           # Configuration files (database, environment, etc.)
│   │   │   ├── controllers      # Handle request and response logic
│   │   │   ├── domain           # Data models and types
│   │   │   │   ├── schemas      # Schema definitions
│   │   │   │   └── types        # TypeScript interfaces for data models
│   │   │   ├── helpers          # Utility functions and reusable code
│   │   │   ├── interfaces       # Infrastructure interfaces (e.g., Database, FileRepository)
│   │   │   ├── adapters         # Implementations of infrastructure interfaces
│   │   │   ├── middlewares      # Middleware for request handling
│   │   │   ├── routes           # Route definitions for API endpoints
│   │   │   ├── services         # Business logic connecting controllers and models
│   │   │   └── validations      # Request validation logic
│   │   └── app.ts               # Initializes Fastify instance, registers plugins, routes, etc.
│   └── functions                # Additional functions package
│       ├── src
│       │   ├── handlers         # Lambda-style handlers for functions
│       │   ├── utils            # Utility functions specific to functions package
│       │   └── config           # Configuration files specific to functions
│       └── index.ts             # Main entry point for the functions package
├── tests                        # Integration, end-to-end, and system tests
│   ├── integration              # Integration tests across packages
│   ├── e2e                      # End-to-end tests simulating real-world scenarios
│   └── mocks                    # Shared mocks for integration and end-to-end tests
├── package.json                 # Root-level package.json for managing workspace dependencies
└── tsconfig.json                # Root-level TypeScript configuration
```

---

## Package Descriptions

### `core/`
This package serves as the core API application, including all business logic, controllers, middleware, and infrastructure adapters.

- **`app.ts`**: Initializes the Fastify instance, sets up plugins, registers routes, and configures server settings.

- **Example**:
  ```typescript
  import Fastify from 'fastify';
  import userRoutes from './src/routes/user.routes';
  
  const app = Fastify({ logger: true });
  app.register(userRoutes, { prefix: '/api/users' });
  
  export default app;
  ```

### `functions/`
The `functions` package includes smaller, modular functions that can be used as standalone handlers, e.g., for serverless platforms.

- **`handlers`**: Contains individual function handlers for specific tasks.
- **Example**: `handlers/generateReport.ts` could define a function that generates a report, called independently from the core API.

### `tests/`
This folder contains integration, end-to-end, and shared mock tests for validating inter-package interactions.

---

## Fastify Setup

Fastify is initialized in the **`app.ts`** file within the `core` package, serving as the entry point for the core API application.

- **File**: `packages/core/app.ts`
- **Purpose**: This file creates and configures the Fastify instance, setting up routes, middlewares, and plugins.
- **Example Configuration**:
  ```typescript
  import Fastify from 'fastify';
  import routes from './src/routes';

  const app = Fastify();

  // Register routes and middleware
  app.register(routes);

  // Start server
  app.listen({ port: 3000 }, (err, address) => {
    if (err) {
      app.log.error(err);
      process.exit(1);
    }
    app.log.info(`Server listening at ${address}`);
  });

  export default app;
  ```

---

## Testing Structure

### In-Source Unit Tests

Each relevant folder within `packages/core/src/` includes a `tests` subfolder for unit tests. This keeps unit tests close to the code they’re testing, ensuring each package remains self-contained.

### Global `tests` Folder for Integration and E2E Tests

The `tests` folder at the root is dedicated to integration and end-to-end tests across packages. This includes shared mocks for consistent and reusable data setups across different packages.

### Example Testing Structure

```plaintext
tests/
├── integration         # Integration tests across multiple packages
├── e2e                 # End-to-end tests for full API workflows
└── mocks               # Shared mocks for integration and E2E tests
```

---

## Installing Dependencies

Since this is a monorepo, dependencies such as TypeScript and Jest can be installed at the root level for shared access across all packages.

### Installing TypeScript and Jest

1. **Install TypeScript and Jest** at the root level:
   ```bash
   npm install --save-dev typescript jest @types/jest ts-jest
   ```

2. **Configure `tsconfig.json` and `jest.config.js`**:
   - Each package should have its own `tsconfig.json` extending from a root `tsconfig.json`.
   - A single global `jest.config.js` can be placed at the root, with optional package-specific configurations if needed.

### Example Root `tsconfig.json`

```json
{
  "compilerOptions": {
    "target": "es2021",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "baseUrl": ".",
    "paths": {
      "*": ["node_modules/*"]
    }
  },
  "include": ["packages/*/src"]
}
```

### Example Global `jest.config.js`

```javascript
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'node',
  testPathIgnorePatterns: ['/node_modules/', '/dist/', '/build/'],
  moduleFileExtensions: ['ts', 'tsx', 'js'],
  moduleDirectories: ['node_modules', 'packages'],
  roots: ['<rootDir>/packages'],
  coverageDirectory: 'coverage',
  collectCoverageFrom: ['packages/**/*.{ts,tsx}', '!packages/**/*.d.ts'],
};
```

---

## Key Best Practices

- **Organize Code by Functionality**: Keep components isolated in purpose and organized within each package.
- **Global Configurations**: Manage shared dependencies and configurations like TypeScript and Jest at the root.
- **Centralized Tests**: Place integration and end-to-end tests in the global `tests` folder.
- **Package-Specific Configurations**: Use individual `tsconfig.json` files for package-specific paths and output configurations.

---

## Summary

This monorepo structure provides a scalable, maintainable approach to building and testing a TypeScript REST API with core functionality and additional functions. By organizing packages separately, it ensures clear organization, test isolation, and flexibility as the project grows.
