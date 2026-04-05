# MySQL Logging Setup Guide

## ✨ What's Included

Your AP Welfare Platform now has **comprehensive MySQL logging** that tracks:

- ✅ **Authentication Events**: Login, Registration, Password Reset, Login Failures
- ✅ **Page Visits**: All navigation and page views with metadata
- ✅ **Chatbot Interactions**: Each step and recommendation request
- ✅ **Form Submissions**: Application forms with field counts
- ✅ **Scheme Views**: Which schemes users browse
- ✅ **Application Starts**: When users begin applications

## 🚀 Quick Start (5 minutes)

### Step 1: Set MySQL Credentials

**Create a `.env` file in the `server` directory:**

```bash
cd server
cp .env.example .env
```

**Edit `.env` with your MySQL credentials:**

```bash
# MySQL Configuration
MYSQL_HOST=localhost
MYSQL_PORT=3306
MYSQL_USER=root
MYSQL_PASSWORD=your_mysql_password  # Change this!
MYSQL_DATABASE=ap_welfare_logs

# Server Configuration
SERVER_PORT=3001
NODE_ENV=development

# CORS
CORS_ORIGIN=http://localhost:8084
```

### Step 2: Initialize Database

```bash
cd server
npm install
npm run setup-db
```

**Expected output:**
```
✅ Database 'ap_welfare_logs' created/verified
✅ activity_logs table created
✅ auth_logs table created
✅ users table created
✨ Database setup completed successfully!
```

### Step 3: Start Backend Server

```bash
npm run dev
```

**Expected output:**
```
✅ MySQL connected successfully
✅ AP Welfare Server running on http://localhost:3001
📊 Database logs endpoint: http://localhost:3001/api/logs
```

### Step 4: Update Frontend Configuration

The frontend already has `.env.local` configured for `http://localhost:3001`

**Verify your `.env.local` has:**
```
VITE_API_URL=http://localhost:3001
VITE_FRONTEND_URL=http://localhost:8084
VITE_LOGGING_ENABLED=true
```

### Step 5: Keep Frontend Server Running

In another terminal:
```bash
npm run dev
```

## 📊 Database Schema

### `activity_logs` Table
Tracks all user activities:
- `user_id`: User identifier
- `activity_type`: page_visit, chatbot_interaction, form_submission, scheme_viewed, application_started
- `page_url`: Current page URL
- `action_details`: JSON with step info and form data
- `metadata`: Additional context (userAgent, referrer, etc.)
- `ip_address`: Client IP
- `timestamp`: When the activity occurred

### `auth_logs` Table
Tracks authentication events:
- `user_id`: User identifier
- `email`: User email
- `event_type`: login, registration, logout, password_reset, login_failed
- `status`: success or failed
- `reason`: Failure reason if applicable
- `ip_address`: Client IP
- `timestamp`: When the event occurred

### `users` Table
Reference table for registered users:
- `user_id`: Unique user identifier
- `email`: User email
- `full_name`: User's full name
- `created_at`: Registration timestamp
- `last_login`: Last login timestamp

## 🔌 API Endpoints

### Log Activity
```bash
POST /api/logs/activity
{
  "userId": "john_doe",
  "activityType": "chatbot_interaction",
  "actionDetails": { "step": 3, "data": {...} },
  "metadata": { "userAgent": "..." }
}
```

### Log Auth Event
```bash
POST /api/logs/auth
{
  "userId": "john_doe",
  "email": "john@example.com",
  "eventType": "login",
  "status": "success"
}
```

### Get Activity Logs
```bash
GET /api/logs/activity?userId=john_doe&limit=50&offset=0
```

### Get Auth Logs
```bash
GET /api/logs/auth?email=john@example.com&eventType=login&status=success
```

### Get Statistics
```bash
GET /api/logs/statistics

Response:
{
  "users": {
    "total_users": 45,
    "unique_emails": 45
  },
  "activityBreakdown": [
    { "activity_type": "page_visit", "count": 324 },
    { "activity_type": "chatbot_interaction", "count": 89 }
  ],
  "recentActivity": { "total": 142 },
  "loginStatistics": {
    "successful_logins": 234,
    "failed_logins": 12
  }
}
```

## 📱 Frontend Logger Functions

### In Your Components:

```typescript
import { 
  logAuthEvent, 
  logActivity, 
  logChatbotInteraction,
  logFormSubmission,
  logSchemeViewed,
  logApplicationStarted
} from '@/lib/logger';

// Log login
await logAuthEvent({
  email: 'user@example.com',
  eventType: 'login',
  status: 'success'
});

// Log page visit
logActivity({
  userId: 'john_doe',
  activityType: 'page_visit',
  pageUrl: window.location.href
});

// Log chatbot interaction
logChatbotInteraction('john_doe', 3, { formData: {...} });

// Log form submission
logFormSubmission('john_doe', 'application_form', formData);

// Log scheme viewed
logSchemeViewed('john_doe', 'scheme_001', 'Rythu Bima');

// Log application started
logApplicationStarted('john_doe', 'scheme_001');
```

## 🔧 Troubleshooting

### MySQL Connection Error
```
Error: connect ECONNREFUSED 127.0.0.1:3306
```
**Solution:**
1. Ensure MySQL is running: `mysql -u root -p`
2. Check credentials in `server/.env`
3. Verify MySQL port (default 3306)

### Database Already Exists
```
Warning: ER_DB_CREATE_EXISTS
```
**This is OK!** The setup script handles this gracefully.

### Logging Not Working in Frontend
**Check:**
1. Backend server is running on port 3001
2. Frontend has `VITE_LOGGING_ENABLED=true`
3. Check browser console for CORS errors
4. Ensure `VITE_API_URL=http://localhost:3001`

## 📈 Viewing Logs

### Query Database Directly
```bash
mysql -u root -p ap_welfare_logs

# View recent activity
SELECT * FROM activity_logs ORDER BY timestamp DESC LIMIT 10;

# View login attempts
SELECT * FROM auth_logs WHERE event_type = 'login' ORDER BY timestamp DESC;

# Count users
SELECT COUNT(DISTINCT user_id) FROM activity_logs;
```

### Use API Endpoints
```bash
# Get all activities
curl http://localhost:3001/api/logs/activity

# Get user statistics
curl http://localhost:3001/api/logs/statistics

# Get login history
curl "http://localhost:3001/api/logs/auth?eventType=login"
```

## 🔐 Security Best Practices

1. **Never commit `.env`** to git - only `.env.example`
2. **Use strong MySQL passwords** in production
3. **Restrict API access** with authentication middleware (planned)
4. **Enable HTTPS** in production
5. **Sanitize user input** before logging sensitive data

## 📦 Production Deployment

**For production:**

1. Use environment variables for all credentials
2. Set `NODE_ENV=production`
3. Enable MySQL SSL connections
4. Add API authentication (JWT tokens)
5. Implement rate limiting
6. Set up log rotation and archival
7. Use a connection pool with appropriate limits

## 🎯 Next Steps

1. ✅ Run `npm run setup-db` to create tables
2. ✅ Start backend: `npm run dev` (in server folder)
3. ✅ Test login/registration to trigger auth logs
4. ✅ Test chatbot to trigger activity logs
5. ✅ View logs via `/api/logs/statistics` endpoint

## 💡 Features Coming Soon

- [ ] Log archival and cleanup jobs
- [ ] Advanced analytics dashboard
- [ ] Real-time log streaming
- [ ] Log export to CSV/PDF
- [ ] Automated backup and recovery
- [ ] Performance metrics and reporting
- [ ] User behavior analytics

---

**Questions?** Check the logs in MySQL or browser console for detailed error messages.
