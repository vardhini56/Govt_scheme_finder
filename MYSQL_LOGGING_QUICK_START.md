# ✨ MySQL Logging System - Complete Setup

## 📋 What's Been Set Up

Your AP Welfare Platform now includes **enterprise-grade MySQL logging** with the following components:

### ✅ Backend Server (Node.js + Express)
- **Location**: `server/` directory
- **Port**: 3001 (configurable via `.env`)
- **Database**: MySQL with 3 tables (activity_logs, auth_logs, users)
- **Dashboard**: Visual monitoring at `/dashboard`

### ✅ Frontend Logger (TypeScript)
- **Location**: `src/lib/logger.ts`
- **Config**: `src/config/logging.ts`
- **Integration**: Already added to LoginPage, RegisterPage, Chatbot

### ✅ Database Schema
- `activity_logs`: Tracks all user activities
- `auth_logs`: Tracks authentication events
- `users`: Reference table for registered users

---

## 🚀 Getting Started (Follow These Steps)

### Step 1: Configure MySQL Credentials
```bash
cd server
cp .env.example .env
# Edit server/.env with your MySQL credentials
nano .env  # or use VS Code to edit
```

**Update these values:**
```
MYSQL_USER=root
MYSQL_PASSWORD=your_mysql_password_here
MYSQL_DATABASE=ap_welfare_logs
```

### Step 2: Install & Setup Database
```bash
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

### Step 4: Start Frontend (New Terminal)
```bash
# In root directory (not server)
npm run dev
```

Should start on `http://localhost:8084`

---

## 📊 Now Test It!

### 1. Visit Dashboard
Open: **http://localhost:3001/dashboard**

You'll see:
- Total users registered
- Successful/failed logins
- Activity breakdown chart
- Recent auth events
- Recent activities

### 2. Trigger Logs
1. Go to **http://localhost:8084/login**
2. Click **"Sign up here"** → navigates to `/register`
3. Fill form and **Register**
   - ✅ Registration event logged
   - ✅ Dashboard updates automatically
4. **Log in** with your new account
   - ✅ Login event logged
   - ✅ Page visit tracked
5. Click **Chatbot** icon (bottom-right)
6. Go through all 5 steps
   - ✅ Each interaction logged
7. Refresh **Dashboard** → See all activities!

---

## 📁 File Structure

```
government_2/
├── server/                          # NEW - Backend server
│   ├── .env                        # YOUR MySQL credentials
│   ├── .env.example                # Template
│   ├── package.json                # Dependencies
│   ├── README.md                   # Quick start guide
│   └── src/
│       ├── server.js               # Express app
│       ├── config/database.js      # MySQL connection
│       ├── controllers/logsController.js  # API logic
│       ├── routes/logs.js          # API endpoints
│       ├── middleware/dashboard.js # Dashboard UI
│       └── migrations/setup.js     # Database schema
│
├── src/
│   ├── lib/logger.ts               # NEW - Frontend logger utility
│   ├── config/logging.ts           # NEW - Logger config
│   ├── pages/
│   │   ├── LoginPage.tsx           # UPDATED - With logging
│   │   └── RegisterPage.tsx        # UPDATED - With logging
│   └── components/
│       └── Chatbot.tsx             # UPDATED - With logging
│
├── MYSQL_LOGGING_SETUP.md          # NEW - Comprehensive guide
├── MYSQL_LOGGING_QUICK_START.md    # This file
└── .env.local                      # NEW - Frontend API config
```

---

## 🔌 Key API Endpoints

### Logging Endpoints
| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/api/logs/activity` | POST | Log user activity |
| `/api/logs/auth` | POST | Log auth events |
| `/api/logs/activity` | GET | Retrieve activity logs |
| `/api/logs/auth` | GET | Retrieve auth logs |
| `/api/logs/statistics` | GET | Get dashboard stats |

### Dashboard & Health
| Endpoint | Purpose |
|----------|---------|
| `/dashboard` | Visual monitoring dashboard |
| `/health` | Server health check |

---

## 📊 What Gets Logged

### Authentication Events
- ✅ User registrations
- ✅ Login attempts (success/failure)
- ✅ Password resets
- ✅ Logouts

### Activity Events
- ✅ Page visits with referrer & user agent
- ✅ Chatbot interactions (each step)
- ✅ Form submissions (fields tracked)
- ✅ Scheme views (which schemes users browse)
- ✅ Application starts (which schemes applied for)

### Log Details Include
- User ID / Email
- IP Address
- Timestamp
- User Agent (for page visits)
- Form data (metadata)
- Success/failure status

---

## 🔧 Frontend Logger Functions

You can use these in any component:

```typescript
import { 
  logAuthEvent,
  logActivity,
  logChatbotInteraction,
  logFormSubmission,
  logSchemeViewed,
  logApplicationStarted
} from '@/lib/logger';

// Examples:
await logAuthEvent({
  email: 'user@example.com',
  eventType: 'login',
  status: 'success'
});

logChatbotInteraction('user_id', 3, { formData: {...} });

logFormSubmission('user_id', 'application', formData);

logSchemeViewed('user_id', 'scheme_001', 'Rythu Bima');
```

---

## ❓ Troubleshooting

### "Can't connect to MySQL server"
1. Ensure MySQL is running:
   ```bash
   mysql -u root -p
   ```
2. Check credentials in `server/.env`
3. Verify MySQL port (default 3306)

### "CORS error in browser console"
1. Check `VITE_API_URL` in `.env.local` is `http://localhost:3001`
2. Ensure backend server is running on port 3001
3. Check `CORS_ORIGIN` in `server/.env` includes your frontend URL

### "Dashboard shows no data"
1. Refresh dashboard page
2. Try registering/logging in again
3. Check browser console for errors
4. Check backend console for API errors

### "Port 3001 already in use"
1. Change port in `server/.env`: `SERVER_PORT=3002`
2. Update `VITE_API_URL` if you change the port

---

## 🎯 Quick Command Reference

### Backend (server directory)
```bash
npm install          # Install dependencies
npm run setup-db     # Create database & tables
npm run dev          # Start development server
npm start            # Start production server
```

### Frontend (root directory)
```bash
npm run dev          # Start development server
npm run build        # Build for production
```

### Database (direct access)
```bash
mysql -u root -p    # Open MySQL CLI
use ap_welfare_logs; # Select database

# Useful queries
SELECT COUNT(*) FROM activity_logs;
SELECT * FROM auth_logs ORDER BY timestamp DESC LIMIT 5;
SELECT DISTINCT user_id FROM activity_logs;
```

---

## 📈 Next Steps

1. ✅ Configure `server/.env` with MySQL credentials
2. ✅ Run `npm install && npm run setup-db` in server folder
3. ✅ Start both servers (backend on 3001, frontend on 8084)
4. ✅ Test login/registration/chatbot
5. ✅ Visit `/dashboard` to view live logs
6. ✅ Read [MYSQL_LOGGING_SETUP.md](./MYSQL_LOGGING_SETUP.md) for production guide

---

## 🎁 What You Have Now

✨ **Enterprise-Grade Logging System**
- Real-time activity tracking
- User behavior analytics
- Authentication audit trail
- Visual dashboard
- Simple REST API
- Easy to extend

🚀 **Production Ready**
- MySQL connection pooling
- Error handling
- CORS support
- Environment configuration
- Database schema with indices

📊 **Comprehensive Analytics**
- User activity breakdown
- Login statistics
- Page visit tracking
- Chatbot interaction metrics
- Form submission tracking

---

**Ready to go!** Follow the 4 steps above and you'll be logging in 5 minutes. 🎉
