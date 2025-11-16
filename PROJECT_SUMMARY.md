# Website Analytics API - Project Overview

## ✅ Completed Features

### 1. Authentication & User Management
- ✅ POST /api/auth/register-user - Register new user account (email/password)
- ✅ POST /api/auth/login - Login and get JWT token
- ✅ JWT-based authentication for secure user management
- ✅ BCrypt password hashing

### 2. API Key Management
- ✅ POST /api/auth/register - Register new application and generate API key (JWT required)
- ✅ GET /api/auth/api-key - Retrieve API key for registered app (JWT required)
- ✅ POST /api/auth/revoke - Revoke API key (JWT required)
- ✅ API key expiration handling
- ✅ Secure API key generation with automatic deactivation of old keys

### 3. Event Data Collection
- ✅ POST /api/analytics/collect - Collect analytics events
- ✅ API key authentication via X-API-Key header
- ✅ Support for events, URLs, referrers, devices, IPs, timestamps, metadata
- ✅ Data integrity and validation
- ✅ High-availability data ingestion

### 4. Analytics & Reporting
- ✅ GET /api/analytics/event-summary - Event summary with time-based and app-based filtering
- ✅ GET /api/analytics/user-stats - User-based statistics with event counts and device details
- ✅ Redis caching for frequently requested data
- ✅ Efficient database queries with proper indexing

### 5. Technical Implementation
- ✅ Spring Boot 3.2.0 with Java 17
- ✅ PostgreSQL database with JPA/Hibernate
- ✅ Redis caching layer
- ✅ JWT token-based authentication
- ✅ Rate limiting with Bucket4j
- ✅ Swagger/OpenAPI documentation
- ✅ Docker containerization
- ✅ Comprehensive exception handling with custom exceptions
- ✅ Request validation
- ✅ Health check endpoint
- ✅ Structured error responses

## Project Structure

```
website-analytics-api/
├── src/
│   ├── main/
│   │   ├── java/com/analytics/
│   │   │   ├── AnalyticsApplication.java
│   │   │   ├── config/          # Configuration classes
│   │   │   ├── constants/       # Application constants
│   │   │   ├── controller/      # REST controllers
│   │   │   ├── dto/             # Data transfer objects
│   │   │   ├── entity/          # JPA entities
│   │   │   ├── exception/       # Exception handlers and custom exceptions
│   │   │   ├── repository/      # Data repositories
│   │   │   ├── security/        # Security configuration and filters
│   │   │   └── service/         # Business logic
│   │   └── resources/
│   │       ├── application.yml
│   │       └── application-prod.yml
│   └── test/                    # Test files
├── pom.xml                      # Maven configuration
├── Dockerfile                   # Docker image definition
├── docker-compose.yml          # Docker Compose setup
├── README.md                   # Comprehensive documentation
├── QUICKSTART.md               # Quick start guide
└── .gitignore                  # Git ignore rules
```

## Database Schema

### Tables
1. **users** - User accounts with email/password authentication
2. **apps** - Registered applications linked to users
3. **api_keys** - API key management with expiration and revocation
4. **analytics_events** - Event data storage
5. **event_metadata** - Flexible metadata storage

### Indexes
- Users: email (unique)
- API keys: key_value (unique), app_id
- Events: event_type, timestamp, user_id, app_id
- Optimized for high-volume queries

## API Endpoints

### Authentication
- `POST /api/auth/register-user` - Register new user account
- `POST /api/auth/login` - Login and get JWT token
- `POST /api/auth/register` - Register application (JWT required)
- `GET /api/auth/api-key?appId={id}` - Get API key (JWT required)
- `POST /api/auth/revoke?apiKey={key}` - Revoke API key (JWT required)

### Analytics
- `POST /api/analytics/collect` - Collect event (API key required)
- `GET /api/analytics/event-summary` - Get event summary with filters
- `GET /api/analytics/user-stats?userId={id}` - Get user stats

### Health & Documentation
- `GET /health` - Health check
- `GET /swagger-ui.html` - Swagger UI
- `GET /api-docs` - OpenAPI JSON

## Deployment

### Docker Compose
```bash
docker-compose up -d
```

### Manual Deployment
1. Set environment variables (JWT_SECRET, DATABASE_URL, REDIS_HOST, etc.)
2. Start PostgreSQL and Redis
3. Run: `mvn spring-boot:run`

### Cloud Platforms
- Railway
- Render
- Heroku
- AWS
- Any platform supporting Docker/Java

## Testing

Run tests:
```bash
mvn test
```

Test coverage includes:
- Controller tests
- Service tests
- Integration tests
- Exception handler tests

## Next Steps for Deployment

1. Set JWT_SECRET environment variable (minimum 256 bits)
2. Configure database connection
3. Set up Redis instance
4. Deploy to cloud platform
5. Update deployment URL in README

## Notes

- All endpoints are documented in Swagger UI
- API keys expire after 365 days (configurable)
- Rate limiting: 100 requests per minute (configurable)
- Cache TTL: 5 minutes (configurable)
- Database auto-creates schema on first run
- JWT tokens expire after 24 hours (configurable)
- Custom exception handling with structured error responses
