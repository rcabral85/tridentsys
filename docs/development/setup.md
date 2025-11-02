# Development Setup Guide

## Prerequisites

Before setting up the Trident Systems platform for development, ensure you have the following installed:

### Required Software

- **Node.js** >= 18.0.0 ([Download](https://nodejs.org/))
- **PostgreSQL** >= 14.0 with PostGIS extension ([Download](https://www.postgresql.org/))
- **Git** >= 2.30.0 ([Download](https://git-scm.com/))
- **Docker** >= 20.10.0 (optional, recommended) ([Download](https://www.docker.com/))

### Development Tools

- **VS Code** (recommended IDE) with extensions:
  - TypeScript and JavaScript Language Features
  - ESLint
  - Prettier
  - PostgreSQL
  - Docker

## Initial Setup

### 1. Clone the Repository

```bash
git clone https://github.com/rcabral85/tridentsys.git
cd tridentsys
```

### 2. Install Dependencies

```bash
# Install all package dependencies
npm install

# Install global development tools (optional)
npm install -g nodemon typescript ts-node
```

### 3. Environment Configuration

```bash
# Copy environment template
cp .env.example .env

# Edit environment variables
nano .env  # or use your preferred editor
```

#### Environment Variables

```bash
# Database Configuration
DATABASE_URL=postgresql://tridentsys:password@localhost:5432/tridentsys_dev
DATABASE_SSL=false
DATABASE_LOGGING=true

# Authentication
JWT_SECRET=your-super-secret-jwt-key-for-development
JWT_EXPIRES_IN=24h
REFRESH_TOKEN_EXPIRES_IN=7d

# API Configuration
API_PORT=3000
API_HOST=localhost
CORS_ORIGIN=http://localhost:3001

# External Services (Development)
GOOGLE_MAPS_API_KEY=your-google-maps-api-key
ESRI_API_KEY=your-esri-api-key

# Email (Development - use Mailtrap or similar)
SMTP_HOST=smtp.mailtrap.io
SMTP_PORT=2525
SMTP_USER=your-mailtrap-user
SMTP_PASS=your-mailtrap-password
SMTP_FROM=dev@tridentsys.ca

# Redis (if using)
REDIS_URL=redis://localhost:6379

# File Storage
UPLOAD_DIR=./uploads
MAX_FILE_SIZE=10485760  # 10MB

# Logging
LOG_LEVEL=debug
LOG_FORMAT=dev
```

## Database Setup

### Option 1: Local PostgreSQL Installation

```bash
# Install PostgreSQL and PostGIS (Ubuntu/Debian)
sudo apt-get update
sudo apt-get install postgresql postgresql-contrib postgis

# Install PostgreSQL and PostGIS (macOS with Homebrew)
brew install postgresql postgis
brew services start postgresql

# Create database and user
sudo -u postgres psql
```

```sql
-- Create database and user
CREATE USER tridentsys WITH PASSWORD 'password';
CREATE DATABASE tridentsys_dev OWNER tridentsys;

-- Connect to the database
\c tridentsys_dev

-- Enable PostGIS extension
CREATE EXTENSION postgis;
CREATE EXTENSION postgis_topology;

-- Grant permissions
GRANT ALL PRIVILEGES ON DATABASE tridentsys_dev TO tridentsys;
GRANT ALL ON SCHEMA public TO tridentsys;

-- Exit
\q
```

### Option 2: Docker Database Setup

```bash
# Start PostgreSQL with PostGIS using Docker
docker run --name tridentsys-db \
  -e POSTGRES_DB=tridentsys_dev \
  -e POSTGRES_USER=tridentsys \
  -e POSTGRES_PASSWORD=password \
  -p 5432:5432 \
  -d postgis/postgis:14-3.2
```

### Run Database Migrations

```bash
# Run all migrations
npm run db:migrate

# Seed database with sample data (optional)
npm run db:seed

# Reset database (drops all data)
npm run db:reset
```

## Development Server

### Start All Services

```bash
# Start all services in development mode
npm run dev

# This will start:
# - API Gateway (port 3000)
# - Frontend Development Server (port 3001)
# - Background Job Processor
# - File Watcher for TypeScript compilation
```

### Start Individual Services

```bash
# API server only
npm run dev:api

# Frontend only
npm run dev:frontend

# Background jobs only
npm run dev:worker
```

## Docker Development Environment

For a complete containerized development environment:

```bash
# Start all services with Docker Compose
docker-compose -f docker-compose.dev.yml up -d

# View logs
docker-compose -f docker-compose.dev.yml logs -f

# Stop services
docker-compose -f docker-compose.dev.yml down

# Rebuild services after code changes
docker-compose -f docker-compose.dev.yml up --build
```

## Code Quality Tools

### Linting and Formatting

```bash
# Run ESLint
npm run lint

# Fix linting issues automatically
npm run lint:fix

# Format code with Prettier
npm run format

# Check formatting
npm run format:check
```

### Type Checking

```bash
# Run TypeScript compiler check
npm run type-check

# Watch for type errors
npm run type-check:watch
```

## Testing

### Running Tests

```bash
# Run all tests
npm test

# Run tests in watch mode
npm run test:watch

# Run specific test file
npm test -- hydrant-hub.test.ts

# Run tests with coverage
npm run test:coverage
```

### Test Database

```bash
# Create test database
npm run db:create:test

# Run migrations on test database
npm run db:migrate:test

# Reset test database
npm run db:reset:test
```

## Common Development Tasks

### Adding a New Module

```bash
# Generate module boilerplate
npm run generate:module -- module-name

# This creates:
# - src/modules/module-name/
# - Controller, Service, Model, Routes
# - Test files
# - Documentation template
```

### Database Migrations

```bash
# Create new migration
npm run migration:create -- add-new-table

# Run pending migrations
npm run db:migrate

# Rollback last migration
npm run db:rollback

# Check migration status
npm run db:status
```

### API Documentation

```bash
# Generate API documentation
npm run docs:generate

# Serve documentation locally
npm run docs:serve

# Documentation will be available at http://localhost:8080
```

## Troubleshooting

### Common Issues

#### Port Already in Use
```bash
# Find process using port 3000
lsof -i :3000

# Kill process
kill -9 <PID>
```

#### Database Connection Issues
```bash
# Test database connection
npm run db:test

# Check PostgreSQL status
sudo systemctl status postgresql  # Linux
brew services list | grep postgresql  # macOS
```

#### Node Modules Issues
```bash
# Clear npm cache and reinstall
npm cache clean --force
rm -rf node_modules package-lock.json
npm install
```

#### TypeScript Compilation Issues
```bash
# Clean TypeScript build cache
npm run clean

# Rebuild everything
npm run build
```

### Development Tips

1. **Use VS Code settings sync** to maintain consistent development environment
2. **Enable auto-save** for faster development cycles
3. **Use Git hooks** for automatic linting and testing
4. **Monitor logs** during development: `npm run logs:watch`
5. **Use database GUI tools** like pgAdmin or DBeaver for easier database management

### Getting Help

- **Documentation**: Check the `/docs` folder for detailed guides
- **Issues**: Create GitHub issues for bugs or feature requests
- **Discussions**: Use GitHub Discussions for questions
- **Team Chat**: Internal team communication channels

## Next Steps

After completing the setup:

1. Read the [Architecture Overview](../architecture/platform-overview.md)
2. Review [Coding Standards](coding-standards.md)
3. Check out [Testing Strategy](testing.md)
4. Explore the [API Reference](../api/)

Welcome to the Trident Systems development team! 🎉