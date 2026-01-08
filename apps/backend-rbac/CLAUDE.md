# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a NestJS TypeScript application for implementing Role-Based Access Control (RBAC). It uses pnpm as the package manager and follows the standard NestJS modular architecture.

## Development Commands

### Installation
```bash
pnpm install
```

### Running the Application
```bash
pnpm run start        # Standard mode
pnpm run start:dev    # Watch mode (recommended for development)
pnpm run start:debug  # Debug mode with watch
pnpm run start:prod   # Production mode
```

The application runs on port 3000 by default (configurable via `PORT` environment variable).

### Building
```bash
pnpm run build        # Compiles TypeScript to dist/
```

### Testing
```bash
pnpm run test         # Run unit tests
pnpm run test:watch   # Run tests in watch mode
pnpm run test:cov     # Run tests with coverage
pnpm run test:e2e     # Run end-to-end tests
pnpm run test:debug   # Run tests in debug mode
```

### Code Quality
```bash
pnpm run lint         # Run ESLint with auto-fix
pnpm run format       # Format code with Prettier
```

## Architecture

### Module System
- **AppModule** (`src/app.module.ts`): Root module that imports all feature modules
- Each feature should be organized as a self-contained module with its own controllers, services, and providers
- Use NestJS dependency injection throughout

### Entry Point
- **main.ts**: Bootstrap file that creates the NestJS application instance
- Default port: 3000 (override with `PORT` environment variable)

### TypeScript Configuration
- Uses `nodenext` module resolution (modern Node.js ESM-compatible)
- Target: ES2023
- Decorators enabled (`experimentalDecorators`, `emitDecoratorMetadata`)
- Strict null checks enabled, but `noImplicitAny` disabled
- Output directory: `./dist`

### Testing Structure
- Unit test files: `*.spec.ts` alongside source files in `src/`
- E2E tests: `test/` directory with `jest-e2e.json` configuration
- Jest is configured with ts-jest transformer

## RBAC Implementation Notes

When implementing RBAC features in this codebase:
- Consider using NestJS Guards for authorization checks
- Decorators should be used for role-based route protection
- Keep role and permission definitions centralized
- Consider implementing a policy-based authorization system for complex permissions
