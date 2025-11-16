# Quick Start Guide

## Prerequisites Setup

1. **Install Java 17+**
   ```bash
   java -version
   ```

2. **Install Maven**
   ```bash
   mvn -version
   ```

3. **Install Docker** (for containerized deployment)
   ```bash
   docker --version
   docker-compose --version
   ```

## Running the Application

### Option 1: Docker Compose (Recommended)

```bash
# Set environment variables (optional, defaults provided)
export JWT_SECRET=your-secret-key-here-min-256-bits
export JWT_EXPIRATION=86400000

# Start all services
docker-compose up -d

# Check logs
docker-compose logs -f app

# Stop services
docker-compose down
```

### Option 2: Local Development

```bash
# Start PostgreSQL
docker run -d --name postgres -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=analytics_db -p 5432:5432 postgres:15-alpine

# Start Redis
docker run -d --name redis -p 6379:6379 redis:7-alpine

# Set environment variables
export JWT_SECRET=your-secret-key-here-min-256-bits
export JWT_EXPIRATION=86400000
export DATABASE_URL=jdbc:postgresql://localhost:5432/analytics_db
export DATABASE_USERNAME=postgres
export DATABASE_PASSWORD=postgres
export REDIS_HOST=localhost
export REDIS_PORT=6379

# Run the application
mvn spring-boot:run
```

## Testing the API

### 1. Access Swagger UI
Open browser: `http://localhost:8080/swagger-ui.html`

### 2. Register a User Account
```bash
curl -X POST http://localhost:8080/api/auth/register-user \
  -H "Content-Type: application/json" \
  -d '{
    "name": "John Doe",
    "email": "john@example.com",
    "password": "password123"
  }'
```

**Response:**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "userId": "uuid",
  "email": "john@example.com",
  "name": "John Doe",
  "tokenType": "Bearer"
}
```

### 3. Login (Alternative to Registration)
```bash
curl -X POST http://localhost:8080/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "john@example.com",
    "password": "password123"
  }'
```

### 4. Register an Application
```bash
curl -X POST http://localhost:8080/api/auth/register \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "My Website",
    "websiteUrl": "https://example.com"
  }'
```

**Response:**
```json
{
  "appId": "uuid",
  "appName": "My Website",
  "apiKey": "ak_...",
  "expiresAt": "2025-02-20T12:34:56",
  "createdAt": "2024-02-20T12:34:56"
}
```

### 5. Collect an Event
```bash
curl -X POST http://localhost:8080/api/analytics/collect \
  -H "X-API-Key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{
    "event": "button_click",
    "url": "https://example.com",
    "device": "mobile",
    "ipAddress": "192.168.1.1",
    "userId": "user123",
    "timestamp": "2024-02-20T12:34:56Z",
    "metadata": {
      "browser": "Chrome",
      "os": "Android",
      "screenSize": "1080x1920"
    }
  }'
```

### 6. Get Event Summary
```bash
curl "http://localhost:8080/api/analytics/event-summary?event=button_click&startDate=2024-02-15&endDate=2024-02-20"
```

### 7. Get User Stats
```bash
curl "http://localhost:8080/api/analytics/user-stats?userId=user123"
```

### 8. Get API Key (if needed)
```bash
curl -X GET "http://localhost:8080/api/auth/api-key?appId=your-app-id" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

### 9. Revoke API Key
```bash
curl -X POST "http://localhost:8080/api/auth/revoke?apiKey=your-api-key" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

## Common Issues

### Issue: Cannot connect to database
**Solution**: Ensure PostgreSQL is running and credentials are correct.

### Issue: Redis connection failed
**Solution**: Ensure Redis is running on the specified port.

### Issue: JWT token invalid or expired
**Solution**: Ensure you're sending the token in the Authorization header as "Bearer <token>". Tokens expire after 24 hours by default. Login again to get a new token.

### Issue: API key authentication fails
**Solution**: Ensure the API key is sent in the `X-API-Key` header or `Authorization: Bearer <key>` header. Check if the API key is active and not expired.

### Issue: Email already registered
**Solution**: Use a different email address or use the login endpoint if you already have an account.

## Authentication Flow

1. **Register User** → Get JWT Token
2. **Login** (if already registered) → Get JWT Token
3. **Register App** (with JWT) → Get API Key
4. **Collect Events** (with API Key) → Events stored
5. **Query Analytics** (public or with API Key) → Get insights

## Next Steps

1. Review the full [README.md](README.md) for detailed documentation
2. Explore the Swagger UI for interactive API testing
3. Check the test files for usage examples
4. Deploy to your preferred cloud platform (Railway, Render, Heroku)
