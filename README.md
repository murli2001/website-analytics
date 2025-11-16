# Website Analytics API

Backend API for Website Analytics built with Spring Boot. Collects analytics events including clicks, page visits, referrer information, and device metrics. Supports high-volume data ingestion with efficient query endpoints.

## Features

### ✅ Authentication & API Key Management
- **POST /api/auth/register-user** - Register a new user account (email/password)
- **POST /api/auth/login** - Login and get JWT token
- **POST /api/auth/register** - Register a new website/app and generate API key (JWT required)
- **GET /api/auth/api-key** - Retrieve the API key for a registered app (JWT required)
- **POST /api/auth/revoke** - Revoke an API key to prevent further use (JWT required)
- JWT-based authentication for secure user management
- Automatic API key expiration handling

### ✅ Event Data Collection
- **POST /api/analytics/collect** - Submit analytics events from websites or mobile apps
- API key authentication via `X-API-Key` header
- Supports events, URLs, referrers, device info, IP addresses, timestamps, and custom metadata
- High-availability data ingestion with data integrity checks

### ✅ Analytics & Reporting
- **GET /api/analytics/event-summary** - Get analytics summary for specific event types with time-based, app-based filtering
- **GET /api/analytics/user-stats** - Get user-based statistics with event counts and device details
- Redis caching for frequently requested data
- Efficient database queries with proper indexing

### ✅ Additional Features
- Rate limiting to prevent abusive requests
- Swagger/OpenAPI documentation
- Docker containerization for easy deployment
- PostgreSQL database with optimized schema
- Redis caching layer
- Test coverage for core functionality
- Global exception handling
- Request validation

## Tech Stack

- **Framework**: Spring Boot 3.2.0
- **Language**: Java 17
- **Database**: PostgreSQL 15
- **Cache**: Redis 7
- **Build Tool**: Maven
- **Authentication**: JWT + API Key
- **Documentation**: SpringDoc OpenAPI (Swagger)
- **Rate Limiting**: Bucket4j
- **Testing**: JUnit 5, Mockito

## Prerequisites

- Java 17 or higher
- Maven 3.6+
- Docker and Docker Compose (for containerized deployment)
- PostgreSQL 15+ (if running without Docker)
- Redis 7+ (if running without Docker)

## Getting Started

### 1. Clone the Repository

```bash
git clone <repository-url>
cd website-analytics-api
```

### 2. Configure Environment Variables

Create a `.env` file in the root directory (optional, defaults are provided):

```env
JWT_SECRET=your-secret-key-here-min-256-bits
JWT_EXPIRATION=86400000
DATABASE_URL=jdbc:postgresql://localhost:5432/analytics_db
DATABASE_USERNAME=postgres
DATABASE_PASSWORD=postgres
REDIS_HOST=localhost
REDIS_PORT=6379
PORT=8080
```

### 4. Run with Docker Compose (Recommended)

```bash
docker-compose up -d
```

This will start:
- PostgreSQL database on port 5432
- Redis cache on port 6379
- Spring Boot application on port 8080

### 5. Run Locally (Without Docker)

#### Start PostgreSQL and Redis

```bash
# PostgreSQL
docker run -d --name postgres -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=analytics_db -p 5432:5432 postgres:15-alpine

# Redis
docker run -d --name redis -p 6379:6379 redis:7-alpine
```

#### Build and Run the Application

```bash
mvn clean install
mvn spring-boot:run
```

Or run the JAR file:

```bash
mvn clean package
java -jar target/website-analytics-api-1.0.0.jar
```

## API Documentation

Once the application is running, access the Swagger UI at:

**http://localhost:8080/swagger-ui.html**

Or view the OpenAPI JSON at:

**http://localhost:8080/api-docs**

## API Endpoints

### Authentication & API Key Management

#### Register User Account
```http
POST /api/auth/register-user
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "password123"
}
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

#### Login
```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "john@example.com",
  "password": "password123"
}
```

#### Register App (JWT Required)
```http
POST /api/auth/register
Authorization: Bearer YOUR_JWT_TOKEN
Content-Type: application/json

{
  "name": "My Website",
  "websiteUrl": "https://example.com"
}
```

**Response:**
```json
{
  "appId": "uuid",
  "appName": "My Website",
  "apiKey": "ak_...",
  "expiresAt": "2025-02-20T12:34:56"
}
```

#### Get API Key (JWT Required)
```http
GET /api/auth/api-key?appId={appId}
Authorization: Bearer YOUR_JWT_TOKEN
```

#### Revoke API Key (JWT Required)
```http
POST /api/auth/revoke?apiKey={apiKey}
Authorization: Bearer YOUR_JWT_TOKEN
```

### Event Collection

#### Collect Analytics Event
```http
POST /api/analytics/collect
X-API-Key: ak_your_api_key_here
Content-Type: application/json

{
  "event": "login_form_cta_click",
  "url": "https://example.com/page",
  "referrer": "https://google.com",
  "device": "mobile",
  "ipAddress": "192.168.1.1",
  "userId": "user123",
  "timestamp": "2024-02-20T12:34:56Z",
  "metadata": {
    "browser": "Chrome",
    "os": "Android",
    "screenSize": "1080x1920"
  }
}
```

### Analytics

#### Get Event Summary
```http
GET /api/analytics/event-summary?event=login_form_cta_click&startDate=2024-02-15&endDate=2024-02-20&appId=xyz123
```

**Response:**
```json
{
  "event": "login_form_cta_click",
  "count": 3400,
  "uniqueUsers": 1200,
  "deviceData": {
    "mobile": 2200,
    "desktop": 1200
  }
}
```

#### Get User Stats
```http
GET /api/analytics/user-stats?userId=user789
```

**Response:**
```json
{
  "userId": "user789",
  "totalEvents": 150,
  "deviceDetails": {
    "browser": "Chrome",
    "os": "Android",
    "screenSize": "1080x1920"
  },
  "ipAddress": "192.168.1.1"
}
```

## Database Schema

### Users Table
- `id` (UUID, Primary Key)
- `email` (String, Unique)
- `password` (String) - Hashed password
- `name` (String)
- `created_at` (Timestamp)
- `updated_at` (Timestamp)

### Apps Table
- `id` (UUID, Primary Key)
- `name` (String)
- `website_url` (String)
- `user_id` (UUID, Foreign Key to Users)
- `created_at` (Timestamp)
- `updated_at` (Timestamp)

### API Keys Table
- `id` (UUID, Primary Key)
- `key_value` (String, Unique, Indexed)
- `app_id` (UUID, Foreign Key)
- `is_active` (Boolean)
- `expires_at` (Timestamp)
- `created_at` (Timestamp)
- `updated_at` (Timestamp)

### Analytics Events Table
- `id` (UUID, Primary Key)
- `event_type` (String, Indexed)
- `url` (String)
- `referrer` (String)
- `device` (String)
- `ip_address` (String)
- `user_id` (String, Indexed)
- `app_id` (UUID, Foreign Key, Indexed)
- `timestamp` (Timestamp, Indexed)
- `created_at` (Timestamp)

### Event Metadata Table
- `event_id` (UUID, Foreign Key)
- `metadata_key` (String)
- `metadata_value` (String)

## Testing

Run all tests:

```bash
mvn test
```

Run specific test class:

```bash
mvn test -Dtest=AnalyticsControllerTest
```

## Docker Deployment

### Build Docker Image

```bash
docker build -t website-analytics-api .
```

### Run with Docker Compose

```bash
docker-compose up -d
```

### Stop Services

```bash
docker-compose down
```

### View Logs

```bash
docker-compose logs -f app
```

## Production Deployment

### Environment Variables for Production

Set these environment variables in your hosting platform:

- `DATABASE_URL` - PostgreSQL connection string
- `DATABASE_USERNAME` - Database username
- `DATABASE_PASSWORD` - Database password
- `REDIS_HOST` - Redis host
- `REDIS_PORT` - Redis port
- `REDIS_PASSWORD` - Redis password (if required)
- `JWT_SECRET` - Secret key for JWT token signing (minimum 256 bits)
- `JWT_EXPIRATION` - JWT token expiration time in milliseconds (default: 86400000 = 24 hours)
- `PORT` - Application port (usually set by hosting platform)

### Deployment Platforms

#### Railway
1. Connect your GitHub repository
2. Add PostgreSQL and Redis services
3. Set environment variables
4. Deploy

#### Render
1. Create a new Web Service
2. Connect your repository
3. Add PostgreSQL and Redis services
4. Configure environment variables
5. Deploy

#### Heroku
1. Create a new app
2. Add PostgreSQL and Redis add-ons
3. Set environment variables
4. Deploy using Git or Heroku CLI

### Deployment URL

Update this section with your actual deployment URL after deploying:

```
https://your-app-name.railway.app
```

or

```
https://your-app-name.onrender.com
```

## Architecture Decisions

### Database Design
PostgreSQL stores all data. Indexes are added on frequently queried columns. JPA/Hibernate handles ORM. Event metadata is stored separately.

### Caching Strategy
Event summary queries are cached in Redis with 5-minute TTL. Cache keys include event type, date range, and app ID.

### Security
JWT tokens handle user authentication. Passwords are hashed with BCrypt. API keys authenticate event collection. Rate limiting prevents abuse.

### Scalability
API is stateless for horizontal scaling. Database indexes and Redis caching improve performance. Connection pooling manages database connections.

## Implementation Notes

### High-Volume Event Ingestion
Database schema uses proper indexing for performance. Connection pooling handles concurrent requests. JPA manages query optimization.

### Analytics Aggregation
Redis caching reduces database load for frequently accessed data. Query results are cached for 5 minutes. Database indexes speed up aggregations.

### API Key Security
API keys are generated using SecureRandom with Base64 encoding. Keys can expire and be revoked when needed.

### Rate Limiting
Bucket4j handles rate limiting using token bucket algorithm. Default is 100 requests per minute, configurable.

### JWT Authentication
JWT tokens are generated and validated using jjwt library. Custom filter processes authentication. Passwords are hashed with BCrypt.

## Future Enhancements

- WebSocket support for real-time analytics
- GraphQL API endpoint
- Advanced analytics features
- Multi-tenant support
- Event streaming
- Dashboard UI
- Export functionality (CSV, JSON, PDF)
- Webhook support
- A/B testing analytics
- Geographic analytics

## Contributing

Contributions are welcome. Please follow standard Git workflow:
1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Open a Pull Request

## License

This project is licensed under the MIT License.

## Support

For issues, questions, or contributions, please open an issue on the GitHub repository.

---

**Website Analytics API - Spring Boot Implementation**

