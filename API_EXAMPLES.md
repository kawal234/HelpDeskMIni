# HelpDesk Mini API - Usage Examples

## Quick Start

1. **Start the server:**
   ```bash
   npm start
   ```

2. **Access the API:**
   - API Documentation: http://localhost:3000/api
   - Health Check: http://localhost:3000/health

## Sample Users

| Role  | Email | Password | Username |
|-------|-------|----------|----------|
| Admin | admin@helpdesk.com | Admin123! | admin |
| Agent | agent1@helpdesk.com | Agent123! | agent1 |
| User  | user1@helpdesk.com | User123! | user1 |

## API Examples

### 1. Authentication

#### Register a new user
```bash
curl -X POST http://localhost:3000/api/auth/register \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: unique-key-123" \
  -d '{
    "username": "newuser",
    "email": "newuser@example.com",
    "password": "Password123!",
    "role": "user"
  }'
```

#### Login
```bash
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "admin@helpdesk.com",
    "password": "Admin123!"
  }'
```

### 2. Ticket Management

#### Create a ticket
```bash
curl -X POST http://localhost:3000/api/tickets \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Idempotency-Key: ticket-123" \
  -d '{
    "title": "API Integration Issue",
    "description": "Having trouble integrating with the new API endpoints",
    "priority": "high"
  }'
```

#### Get all tickets
```bash
curl -X GET http://localhost:3000/api/tickets \
  -H "Authorization: Bearer YOUR_TOKEN"
```

#### Get tickets with filtering
```bash
curl -X GET "http://localhost:3000/api/tickets?status=open&priority=high&limit=10&offset=0" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

#### Get specific ticket
```bash
curl -X GET http://localhost:3000/api/tickets/1 \
  -H "Authorization: Bearer YOUR_TOKEN"
```

#### Update ticket
```bash
curl -X PATCH http://localhost:3000/api/tickets/1 \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "status": "in_progress",
    "priority": "medium",
    "version": 1
  }'
```

#### Add comment to ticket
```bash
curl -X POST http://localhost:3000/api/tickets/1/comments \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "content": "I have started working on this issue"
  }'
```

#### Search tickets
```bash
curl -X GET "http://localhost:3000/api/tickets/search?q=login%20error" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

#### Get SLA breached tickets
```bash
curl -X GET http://localhost:3000/api/tickets/sla-breached \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### 3. User Management (Admin only)

#### Get all users
```bash
curl -X GET http://localhost:3000/api/users \
  -H "Authorization: Bearer ADMIN_TOKEN"
```

#### Update user role
```bash
curl -X PATCH http://localhost:3000/api/users/2/role \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer ADMIN_TOKEN" \
  -d '{
    "role": "agent"
  }'
```

## Key Features Demonstrated

### 1. **Role-Based Access Control (RBAC)**
- Users can only access their own tickets
- Agents can access and modify any ticket
- Admins have full system access

### 2. **SLA Tracking**
- Automatic SLA calculation based on priority
- SLA breach detection and reporting
- Configurable SLA hours per priority level

### 3. **Optimistic Locking**
- All ticket updates require version number
- Prevents concurrent modification conflicts
- Returns 409 Conflict if version mismatch

### 4. **Idempotency**
- POST requests support Idempotency-Key header
- Safe to retry requests without side effects
- Prevents duplicate resource creation

### 5. **Rate Limiting**
- 60 requests per minute per IP
- Stricter limits for authentication endpoints
- Returns 429 Too Many Requests when exceeded

### 6. **Search & Filtering**
- Full-text search across tickets and comments
- Filter by status, priority, assigned user
- Pagination support with limit/offset

### 7. **Threaded Comments**
- Support for comment replies
- Complete audit trail
- Real-time ticket history

## Error Handling

All errors follow a consistent format:

```json
{
  "error": "Error Type",
  "message": "Human-readable error message",
  "timestamp": "2024-01-01T00:00:00.000Z",
  "path": "/api/tickets",
  "method": "POST"
}
```

## Configuration

Environment variables in `.env`:

```env
PORT=3000
NODE_ENV=development
JWT_SECRET=your-secret-key
JWT_EXPIRES_IN=24h
DATABASE_PATH=./database/helpdesk.db
RATE_LIMIT_WINDOW_MS=60000
RATE_LIMIT_MAX_REQUESTS=60
DEFAULT_SLA_HOURS=24
PRIORITY_SLA_HOURS_HIGH=4
PRIORITY_SLA_HOURS_MEDIUM=12
PRIORITY_SLA_HOURS_LOW=48
```

## Database Schema

The system uses SQLite with the following main tables:
- `users` - User accounts and roles
- `tickets` - Support tickets with SLA tracking
- `comments` - Threaded comments system
- `ticket_history` - Complete audit trail
- `idempotency_keys` - Idempotency tracking

## Testing

Run the test suite:
```bash
npm test
```

Run the demo:
```bash
node demo.js
```

