# Implementation Plan: ExpressJS CRUD API for User Management

## Overview
Build a production-ready ExpressJS backend API with TypeScript to manage User resources (CRUD operations), using SQLite with Prisma ORM, including basic token authentication and comprehensive testing.

## Technology Stack
- **Runtime**: Node.js with TypeScript
- **Framework**: Express.js
- **Database**: SQLite (file-based, zero-config)
- **ORM**: Prisma (type-safe database access)
- **Validation**: Zod (runtime type validation)
- **Authentication**: JWT (JSON Web Tokens) for API authentication
- **Testing**: Jest + Supertest (integration tests)
- **Dev Tools**: tsx (TypeScript execution), nodemon (development)

## User Resource Schema
```typescript
{
  id: string (UUID, auto-generated)
  firstName: string (required, 1-100 chars)
  lastName: string (required, 1-100 chars)
  age: number (required, 1-150)
  createdAt: DateTime (auto-generated)
  updatedAt: DateTime (auto-updated)
}
```

## API Endpoints

### Authentication
- `POST /api/auth/register` - Register new API user (get auth token)
- `POST /api/auth/login` - Login with credentials (get auth token)

### User CRUD (Protected - requires Bearer token)
- `POST /api/users` - Create a new user
- `GET /api/users` - List users with filters (?firstName=, ?lastName=, ?minAge=, ?maxAge=, ?limit=, ?offset=)
- `GET /api/users/:id` - Get user by ID
- `PUT /api/users/:id` - Update user (full update)
- `PATCH /api/users/:id` - Partial update user
- `DELETE /api/users/:id` - Delete user

## Project Structure
```
src/problem5/
├── src/
│   ├── config/
│   │   ├── database.ts          # Prisma client initialization
│   │   └── env.ts               # Environment configuration
│   ├── middleware/
│   │   ├── auth.middleware.ts   # JWT authentication middleware
│   │   ├── error.middleware.ts  # Global error handler
│   │   └── validation.middleware.ts # Request validation
│   ├── routes/
│   │   ├── auth.routes.ts       # Authentication routes
│   │   └── user.routes.ts       # User CRUD routes
│   ├── controllers/
│   │   ├── auth.controller.ts   # Auth logic
│   │   └── user.controller.ts   # User CRUD logic
│   ├── services/
│   │   ├── auth.service.ts      # Auth business logic
│   │   └── user.service.ts      # User business logic
│   ├── validators/
│   │   └── user.validator.ts    # Zod schemas for validation
│   ├── types/
│   │   └── index.ts             # TypeScript type definitions
│   ├── utils/
│   │   ├── jwt.util.ts          # JWT helper functions
│   │   └── error.util.ts        # Custom error classes
│   ├── app.ts                   # Express app setup
│   └── server.ts                # Server entry point
├── prisma/
│   ├── schema.prisma            # Prisma schema definition
│   └── migrations/              # Database migrations
├── tests/
│   ├── integration/
│   │   ├── auth.test.ts         # Auth endpoint tests
│   │   └── users.test.ts        # User CRUD tests
│   └── setup.ts                 # Test configuration
├── .env.example                 # Environment variables template
├── .gitignore
├── package.json
├── tsconfig.json
├── jest.config.js
└── README.md                    # Setup and usage documentation
```

## Implementation Steps

### 1. Project Initialization
- Create package.json with all dependencies
- Configure TypeScript (tsconfig.json)
- Configure Jest for testing
- Create .env.example and .gitignore
- Initialize Prisma

### 2. Database Setup
- Define Prisma schema (User model + ApiUser model for auth)
- Create initial migration
- Generate Prisma client
- Create database configuration

### 3. Core Infrastructure
- Environment configuration loader
- Custom error classes (NotFoundError, ValidationError, UnauthorizedError)
- JWT utility functions (generate, verify)
- Global error middleware
- Request validation middleware

### 4. Authentication Layer
- Auth service (register, login, password hashing with bcrypt)
- Auth controller
- Auth routes
- Auth middleware (JWT verification)

### 5. User CRUD Implementation
- User validators (Zod schemas for create/update/filters)
- User service (business logic with Prisma)
- User controller (request/response handling)
- User routes (with auth middleware)

### 6. Express App Assembly
- App setup (app.ts): middleware chain, routes, error handling
- Server setup (server.ts): start server with graceful shutdown

### 7. Testing Suite
- Test setup (in-memory SQLite database)
- Auth integration tests (register, login, token validation)
- User CRUD integration tests (all endpoints with filters)
- Error handling tests

### 8. Documentation
- README.md with:
  - Prerequisites
  - Installation instructions
  - Environment configuration
  - Database setup (migrations)
  - Running the application
  - API documentation with examples
  - Testing instructions

## Key Features

### Validation (Zod)
- Input validation for all request bodies
- Query parameter validation for filters
- Type-safe validation with automatic TypeScript inference

### Error Handling
- Custom error classes with proper HTTP status codes
- Global error middleware
- Consistent error response format:
```json
{
  "error": {
    "message": "Error message",
    "code": "ERROR_CODE",
    "details": {}
  }
}
```

### Authentication
- JWT-based authentication
- Password hashing with bcrypt
- Protected routes via middleware
- Token expiration (configurable)

### Filtering
- Query parameters for user listing:
  - `firstName`: Filter by first name (partial match)
  - `lastName`: Filter by last name (partial match)
  - `minAge`: Minimum age filter
  - `maxAge`: Maximum age filter
  - `limit`: Pagination limit (default: 50, max: 100)
  - `offset`: Pagination offset (default: 0)

### HTTP Status Codes
- 200: Success (GET, PUT, PATCH)
- 201: Created (POST)
- 204: No Content (DELETE)
- 400: Bad Request (validation errors)
- 401: Unauthorized (missing/invalid token)
- 404: Not Found (resource not found)
- 500: Internal Server Error

## Dependencies

### Production
- express
- @prisma/client
- zod
- jsonwebtoken
- bcrypt
- dotenv
- cors
- helmet (security headers)
- express-rate-limit (rate limiting)

### Development
- typescript
- tsx (TypeScript execution)
- @types/node, @types/express, @types/jsonwebtoken, @types/bcrypt
- prisma (CLI)
- jest, @types/jest, ts-jest
- supertest, @types/supertest
- nodemon

## Environment Variables
```
# Server
PORT=3000
NODE_ENV=development

# Database
DATABASE_URL="file:./dev.db"

# JWT
JWT_SECRET=your-secret-key-change-in-production
JWT_EXPIRATION=24h

# Rate Limiting
RATE_LIMIT_WINDOW_MS=900000
RATE_LIMIT_MAX_REQUESTS=100
```

## Testing Strategy
- Integration tests for all endpoints
- Test database isolation (separate SQLite file)
- Test authentication flow
- Test all CRUD operations
- Test filtering and pagination
- Test error scenarios (404, 400, 401)
- Aim for >80% code coverage

## Success Criteria
- ✅ All 5 CRUD operations functional
- ✅ SQLite database with Prisma ORM
- ✅ TypeScript with strict type checking
- ✅ Input validation with Zod
- ✅ JWT authentication protecting routes
- ✅ Comprehensive error handling
- ✅ List endpoint with working filters
- ✅ Integration tests passing
- ✅ README with clear setup instructions
- ✅ Proper HTTP status codes
- ✅ Clean, maintainable code structure
