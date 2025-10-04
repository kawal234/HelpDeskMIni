# HelpDesk Mini - Ticketing System API

A robust, full-featured ticketing system API that supports multiple user roles, threaded communication, and critical service level compliance.

## 🚀 Features

### Core Functionality
- **Ticket Management**: Create, read, update, and track support tickets
- **Threaded Communication**: Comments system with parent-child relationships
- **SLA Tracking**: Automatic SLA calculation and breach detection
- **Role-Based Access Control (RBAC)**: User, Agent, and Admin roles
- **Optimistic Locking**: Prevents concurrent update conflicts
- **Full-Text Search**: Search across tickets and comments

### API Robustness Standards
- **Rate Limiting**: 60 requests per minute per user
- **Idempotency**: Safe retry for POST requests using Idempotency-Key header
- **Pagination**: Standardized limit/offset pagination
- **Uniform Error Handling**: Consistent JSON error responses
- **Comprehensive Audit Trail**: Complete ticket history tracking

## 🛠️ Technology Stack

- **Node.js** with Express.js
- **SQLite** database with FTS5 for full-text search
- **JWT** for authentication
- **bcryptjs** for password hashing
- **express-rate-limit** for rate limiting
- **express-validator** for input validation

## 📋 Prerequisites

- Node.js (v14 or higher)
- npm or yarn

## 🚀 Quick Start

### **Prerequisites**
- Node.js (v14 or higher)
- npm or yarn

### **Installation & Setup**

1. **Navigate to the project directory:**
   ```bash
   cd /Users/kawaljotsingh01/Documents/HackathonProject
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

3. **Set up environment variables:**
   ```bash
   cp env.example .env
   # The .env file is already configured with default values
   ```

4. **Initialize the database:**
   ```bash
   npm run migrate
   ```
   This will create the database schema and populate it with sample data.

5. **Start the server:**
   ```bash
   npm start
   ```
   
   For development with auto-reload:
   ```bash
   npm run dev
   ```

### **Access the Application**

Once the server is running, you can access:

- **🌐 Web Interface:** http://localhost:3000
- **📚 API Documentation:** http://localhost:3000/api
- **🏥 Health Check:** http://localhost:3000/health
- **🔧 Debug Page:** http://localhost:3000/debug.html

### **Default Login Credentials**

| Role  | Email | Password | Username |
|-------|-------|----------|----------|
| Admin | admin@helpdesk.com | Admin123! | admin |
| Agent | agent1@helpdesk.com | Agent123! | agent1 |
| User  | user1@helpdesk.com | User123! | user1 |
| User  | user2@helpdesk.com | User123! | user2 |

## 🎯 How to Use the Application

### **Web Interface Usage**

1. **Login:**
   - Open http://localhost:3000 in your browser
   - Use any of the provided credentials above
   - Click "Login" button

2. **View Tickets:**
   - After login, you'll see all tickets
   - Each ticket shows status, priority, and SLA information
   - Tickets are color-coded by priority

3. **Create New Ticket:**
   - Click the "+ New Ticket" button
   - Fill in the title, description, and priority
   - Click "Create Ticket" to submit

4. **Role-Based Access Control:**

   **👤 User Role:**
   - Can only view and edit their own tickets
   - Can create new tickets
   - Can only edit tickets with "open" status
   - Cannot assign or delete tickets

   **🎯 Agent Role:**
   - Can view all tickets assigned to them
   - Can view unassigned tickets
   - Can edit any ticket
   - Can assign tickets to themselves or others
   - Can view SLA breached tickets
   - Cannot delete tickets

   **🔧 Admin Role:**
   - Full access to all tickets
   - Can view all tickets, SLA breached tickets
   - Can manage users and system settings
   - Can delete tickets
   - Can view system statistics
   - Complete system control

### **API Usage**

The application also provides a REST API for programmatic access:

```bash
# Login and get token
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "admin@helpdesk.com", "password": "Admin123!"}'

# Create a ticket
curl -X POST http://localhost:3000/api/tickets \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Idempotency-Key: unique-key-123" \
  -d '{"title": "API Test", "description": "Testing via API", "priority": "high"}'

# Get all tickets
curl -X GET http://localhost:3000/api/tickets \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### **Available Scripts**

```bash
# Start the server
npm start

# Start with auto-reload (development)
npm run dev

# Run database migrations
npm run migrate

# Run tests
npm test
```

## 📚 API Endpoints

### Authentication
- `POST /api/auth/register` - Register a new user
- `POST /api/auth/login` - Login user
- `GET /api/auth/profile` - Get user profile
- `PUT /api/auth/profile` - Update user profile
- `PUT /api/auth/change-password` - Change password

### Tickets
- `POST /api/tickets` - Create a new ticket
- `GET /api/tickets` - Get all tickets (with filtering)
- `GET /api/tickets/search` - Search tickets
- `GET /api/tickets/sla-breached` - Get SLA breached tickets
- `GET /api/tickets/:id` - Get specific ticket
- `PATCH /api/tickets/:id` - Update ticket
- `POST /api/tickets/:id/comments` - Add comment to ticket
- `GET /api/tickets/:id/history` - Get ticket history

### Users (Admin only)
- `GET /api/users` - Get all users
- `GET /api/users/:id` - Get specific user
- `PATCH /api/users/:id/role` - Update user role
- `DELETE /api/users/:id` - Delete user

## 🔒 Role-Based Access Control

### User Role
- Can create tickets
- Can view and update their own tickets
- Can add comments to their tickets
- Cannot assign tickets to others

### Agent Role
- All user permissions
- Can view and update any ticket
- Can assign tickets to themselves or others
- Can change ticket status and priority

### Admin Role
- All agent permissions
- Can manage users (create, update, delete)
- Can change user roles
- Full system access

## ⏱️ SLA Configuration

The system automatically calculates SLA due dates based on ticket priority:

| Priority | SLA Hours | Default |
|----------|-----------|---------|
| Urgent   | 4 hours   | 4 hours |
| High     | 4 hours   | 4 hours |
| Medium   | 12 hours  | 12 hours|
| Low      | 48 hours  | 48 hours|

SLA breach detection runs automatically every 5 minutes.

## 🔄 Optimistic Locking

All ticket updates require the current version number to prevent conflicts:

```json
{
  "title": "Updated title",
  "version": 3
}
```

If the version doesn't match, the server returns a 409 Conflict error.

## 🔍 Search Functionality

Search across tickets and comments using the `/api/tickets/search` endpoint:

```
GET /api/tickets/search?q=login%20error&limit=10&offset=0
```

## 📄 Pagination

All list endpoints support pagination:

```
GET /api/tickets?limit=20&offset=40
```

## 🔑 Idempotency

POST requests support idempotency using the `Idempotency-Key` header:

```bash
curl -X POST http://localhost:3000/api/tickets \
  -H "Idempotency-Key: unique-key-123" \
  -H "Authorization: Bearer your-token" \
  -d '{"title": "New ticket", "description": "Description"}'
```

## 🚦 Rate Limiting

- **General**: 60 requests per minute per IP
- **Authentication**: 5 attempts per 15 minutes
- **Ticket Creation**: 10 tickets per minute

## 📊 Error Handling

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

## 🧪 Testing

Run the test suite:

```bash
npm test
```

## 📁 Project Structure

```
src/
├── controllers/     # Request handlers
├── middleware/      # Authentication, validation, rate limiting
├── models/         # Data models
├── routes/         # API routes
├── services/       # Business logic
├── utils/          # Utility functions
├── database/       # Database connection and migrations
└── server.js       # Main application file
```

## 🔧 Configuration

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

## 🚀 Deployment

1. Set production environment variables
2. Run database migrations
3. Start the server with PM2 or similar process manager
4. Set up reverse proxy (nginx) for production
5. Configure SSL certificates

## 📝 License

MIT License - see LICENSE file for details

## 🔧 Troubleshooting

### **Common Issues**

**1. Port Already in Use**
```bash
Error: listen EADDRINUSE: address already in use :::3000
```
**Solution:** Kill any existing processes on port 3000
```bash
pkill -f "node src/server.js"
# or
lsof -ti:3000 | xargs kill -9
```

**2. Database Connection Issues**
```bash
Error: SQLITE_ERROR: no such table
```
**Solution:** Run the migration
```bash
npm run migrate
```

**3. CORS Errors in Browser**
```bash
Access to fetch at 'http://localhost:3000/api/auth/login' has been blocked by CORS policy
```
**Solution:** The server is configured to handle CORS. Make sure you're accessing via http://localhost:3000

**4. Login Button Not Working**
- Check browser console for JavaScript errors
- Ensure the server is running
- Try refreshing the page (Ctrl+F5 or Cmd+Shift+R)

**5. CSP (Content Security Policy) Errors**
- The server is configured to allow inline scripts
- If you see CSP errors, restart the server

### **Debug Commands**

```bash
# Check if server is running
curl http://localhost:3000/health

# Test login API
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "admin@helpdesk.com", "password": "Admin123!"}'

# Check server logs
# Look at the terminal where you ran 'npm start'
```

### **Reset Everything**

If you encounter persistent issues:

```bash
# Stop the server
pkill -f "node src/server.js"

# Remove database
rm -f database/helpdesk.db

# Reinstall dependencies
rm -rf node_modules
npm install

# Recreate database
npm run migrate

# Start server
npm start
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests
5. Submit a pull request

## 📞 Support

For support and questions, please open an issue in the repository.
