# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Development Commands

### Development Server

- `npm run dev` - Start development server with hot reload using Node's `--watch` flag
- `npm start` - Run the production server (uses `src/index.js`)

### Code Quality

- `npm run lint` - Run ESLint to check for code issues
- `npm run lint:fix` - Run ESLint and automatically fix issues
- `npm run format` - Format code using Prettier
- `npm run format:check` - Check if code is formatted correctly without making changes

### Database Operations

- `npm run db:generate` - Generate Drizzle database schema migrations
- `npm run db:migrate` - Run pending database migrations
- `npm run db:studio` - Open Drizzle Studio for database management

### Environment Setup

1. Copy `.env.example` to `.env` and configure:
   - `PORT` (default: 3000)
   - `DATABASE_URL` (Neon PostgreSQL connection string)
   - `NODE_ENV` (development/production)
   - `LOG_LEVEL` (info/debug/error)

## Architecture Overview

### Tech Stack

- **Runtime**: Node.js with ES modules (`"type": "module"`)
- **Framework**: Express.js 5.x
- **Database**: PostgreSQL via Neon with Drizzle ORM
- **Authentication**: JWT tokens stored in HTTP-only cookies
- **Validation**: Zod schemas
- **Security**: Helmet, CORS, bcrypt password hashing
- **Logging**: Winston with file and console transports

### Project Structure

This is a layered Node.js API following MVC-style architecture with clear separation of concerns:

```
src/
├── config/          # Configuration modules (database, logger)
├── controllers/     # HTTP request handlers
├── models/          # Database schema definitions (Drizzle)
├── routes/          # Express route definitions
├── services/        # Business logic layer
├── utils/           # Utility functions (JWT, cookies, formatting)
├── validations/     # Zod validation schemas
├── middlewares/     # Custom middleware (if any)
├── app.js          # Express app configuration
├── server.js       # Server startup
└── index.js        # Application entry point
```

### Import Mapping

The project uses Node.js import maps for clean imports:

- `#config/*` → `./src/config/*`
- `#controllers/*` → `./src/controllers/*`
- `#models/*` → `./src/models/*`
- `#routes/*` → `./src/routes/*`
- `#services/*` → `./src/services/*`
- `#middlewares/*` → `./src/middlewares/*`
- `#utils/*` → `./src/utils/*`
- `#validations/*` → `./src/validations/*`

### Database Architecture

- **ORM**: Drizzle with Neon PostgreSQL serverless
- **Schema**: Defined in `src/models/` using Drizzle's `pgTable`
- **Current Models**: Users table with id, name, email, password, role, timestamps
- **Migrations**: Generated and managed via `drizzle-kit`

### Authentication Flow

1. **Registration/Login**: Zod validation → password hashing → JWT creation → HTTP-only cookie storage
2. **JWT Configuration**: Stored in secure, HTTP-only cookies with 15-minute expiration
3. **Password Security**: bcrypt with 10 rounds for hashing
4. **User Roles**: Supports 'user' and 'admin' roles with default 'user'

### Logging Strategy

- **Production**: File-based logging (`logs/error.log`, `logs/combined.log`)
- **Development**: Console logging with colors
- **Service Context**: All logs tagged with `service: 'acquisition-api'`
- **Request Logging**: Morgan middleware integrated with Winston

### API Endpoints

Current authentication endpoints:

- `POST /api/auth/sign-up` - User registration (implemented)
- `POST /api/auth/sign-in` - User login (controller exists, route placeholder)
- `POST /api/auth/sign-out` - User logout (controller exists, route placeholder)
- `GET /health` - Health check endpoint
- `GET /` - Basic status endpoint

### Development Notes

- **Entry Point**: Application starts from `src/index.js` → loads env → starts server
- **Error Handling**: Centralized error handling with proper HTTP status codes
- **Security Headers**: Helmet middleware for security headers
- **CORS**: Enabled for cross-origin requests
- **Cookie Security**: Production-safe cookie configuration with secure/sameSite settings
