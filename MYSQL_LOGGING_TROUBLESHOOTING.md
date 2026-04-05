# 🔧 MySQL Logging - Troubleshooting Guide

## Common Issues & Solutions

### 1️⃣ MySQL Connection Error
```
Error: connect ECONNREFUSED 127.0.0.1:3306
```

**Cause**: MySQL server is not running

**✅ Solution**:
```bash
# Windows
net start MySQL80
# or find MySQL in Services and start it

# macOS
brew services start mysql

# Linux
sudo service mysql start
```

**Verify**: 
```bash
mysql -u root -p
# If you can log in, MySQL is running
exit
```

---

### 2️⃣ MySQL Authentication Failed
```
Error: ER_ACCESS_DENIED_FOR_USER 'root'@'localhost'
```

**Cause**: Wrong password in `server/.env`

**✅ Solution**:
```bash
# Edit server/.env
MYSQL_PASSWORD=your_actual_mysql_password

# Test your password directly
mysql -u root -p
# Enter your password
# If it works, MySQL credentials are correct
```

---

### 3️⃣ Database Already Exists Warning
```
ER_DB_CREATE_EXISTS: Database 'ap_welfare_logs' already exists
```

**This is OK!** ✅ The setup script handles this gracefully.

The script will verify tables exist and create them if needed.

---

### 4️⃣ CORS Error in Browser Console
```
Access to XMLHttpRequest blocked by CORS policy
```

**Cause**: Frontend URL not in CORS whitelist

**✅ Solution**:
```bash
# Edit server/.env
CORS_ORIGIN=http://localhost:8084

# If using different port:
CORS_ORIGIN=http://localhost:8085
```

**Then restart backend server**

---

### 5️⃣ Frontend Can't Reach Backend
```
Failed to fetch - net::ERR_CONNECTION_REFUSED
```

**Cause**: Backend API URL is wrong

**✅ Solution**:
```bash
# Edit .env.local
VITE_API_URL=http://localhost:3001

# Verify backend is running
curl http://localhost:3001/health
# Should return: {"status":"healthy","database":"connected"}
```

---

### 6️⃣ Port Already in Use
```
Error: listen EADDRINUSE :::3001
```

**Cause**: Another process using port 3001

**✅ Solution**:

**Option A - Change Port**:
```bash
# Edit server/.env
SERVER_PORT=3002

# Update frontend config:
# Edit .env.local
VITE_API_URL=http://localhost:3002
```

**Option B - Kill Process**:
```bash
# Windows
netstat -ano | findstr :3001
taskkill /PID <PID> /F

# macOS/Linux
lsof -i :3001
kill -9 <PID>
```

---

### 7️⃣ npm install Fails
```
npm ERR! peer dep missing
```

**✅ Solution**:
```bash
# Clear npm cache
npm cache clean --force

# Reinstall
npm install

# If still fails:
rm -rf node_modules package-lock.json
npm install
```

---

### 8️⃣ Dashboard Shows No Data
```
Dashboard is empty or shows "Loading..."
```

**✅ Check**:

1. Backend is running:
```bash
curl http://localhost:3001/health
# Should return: {"status":"healthy","database":"connected"}
```

2. Database has tables:
```bash
mysql -u root -p ap_welfare_logs
SHOW TABLES;
# Should list: activity_logs, auth_logs, users
```

3. Are you registered/logged in?
- No data until someone logs in/registers
- Try registering a new account
- Refresh dashboard

---

### 9️⃣ Logs Not Appearing in Database
```
Dashboard and database are empty after logging in
```

**✅ Troubleshoot**:

1. Open browser console (F12 → Console)
2. Look for any red error messages
3. If you see fetch errors, check:
   - Backend is running (`npm run dev`)
   - CORS configuration is correct
   - API URL is correct in `.env.local`

4. Check backend console for errors:
```bash
# In server terminal, look for:
# ❌ Errors in the logs
# ✅ "Activity log created" messages
```

---

### 🔟 "Cannot find module" Errors
```
Error: Cannot find module '@/lib/logger'
```

**Cause**: TypeScript path aliases not configured

**✅ Solution**: Restart Vite dev server
```bash
# Stop: Ctrl+C
npm run dev
```

---

## 📊 Debug Commands

### Test Backend Health
```bash
curl http://localhost:3001/health
# Expected: {"status":"healthy","database":"connected"}
```

### Get All Activity Logs
```bash
curl http://localhost:3001/api/logs/activity
# Returns: {"total":10,"logs":[...]}
```

### Get All Auth Logs
```bash
curl http://localhost:3001/api/logs/auth
# Returns: {"total":5,"logs":[...]}
```

### Get Statistics
```bash
curl http://localhost:3001/api/logs/statistics
# Returns: detailed analytics
```

---

## 🐛 Check Logs

### Frontend Logs (Browser Console)
```javascript
// Open browser F12 → Console
// Look for:
// ✅ Successful fetch calls
// ❌ CORS errors
// ❌ Network errors
```

### Backend Logs (Terminal)
```bash
# Where you ran "npm run dev"
# Look for:
# ✅ "✅ MySQL connected successfully"
# ❌ Connection errors
# ❌ SQL errors
```

### MySQL Logs
```bash
# Check MySQL error log
mysql -u root -p
SHOW ENGINE INNODB STATUS;
```

---

## 🧪 Manual Testing

### Test 1: Register New User
1. Open `http://localhost:8084/login`
2. Click "Sign up here"
3. Fill form: Name, Phone, Address, Email, Password
4. Click "Create Account"
5. ✅ Check MySQL:
```bash
mysql -u root -p ap_welfare_logs
SELECT * FROM auth_logs WHERE event_type = 'registration' ORDER BY timestamp DESC LIMIT 1;
```

### Test 2: Check Login Logs
1. At login page, enter credentials
2. Click "Sign In"
3. ✅ Check MySQL:
```bash
mysql -u root -p ap_welfare_logs
SELECT * FROM auth_logs WHERE event_type = 'login' ORDER BY timestamp DESC LIMIT 1;
```

### Test 3: Test Chatbot Interaction
1. Click chatbot button (bottom-right)
2. Click through all 5 steps
3. ✅ Check MySQL:
```bash
mysql -u root -p ap_welfare_logs
SELECT * FROM activity_logs WHERE activity_type = 'chatbot_interaction' ORDER BY timestamp DESC LIMIT 5;
```

---

## 💻 System Information to Provide When Reporting Issues

```bash
# Node version
node --version

# npm version
npm --version

# MySQL version
mysql --version

# Backend error (if any)
# Paste from terminal where "npm run dev" runs in server/

# Frontend error (if any)
# Paste from browser F12 → Console

# .env configuration (hide passwords)
# Paste from server/.env (REMOVE PASSWORD)
```

---

## 📞 Support Checklist

When asking for help, provide:

- [ ] What did you do? (steps to reproduce)
- [ ] What happened? (error messages)
- [ ] What should happen? (expected behavior)
- [ ] Node version (`node --version`)
- [ ] npm version (`npm --version`)
- [ ] MySQL version (`mysql --version`)
- [ ] Are both servers running? (backend & frontend)
- [ ] What's in browser console? (F12 → Console)
- [ ] What's in terminal? (backend server errors)

---

## ✅ Quick Verification Checklist

```bash
# 1. MySQL running?
mysql -u root -p
# Can you login? ✅

# 2. Database exists?
SHOW DATABASES;
# See ap_welfare_logs? ✅

# 3. Tables exist?
USE ap_welfare_logs;
SHOW TABLES;
# See activity_logs, auth_logs, users? ✅

# 4. Backend running?
curl http://localhost:3001/health
# Returns healthy? ✅

# 5. Frontend accessible?
curl http://localhost:8084
# Returns HTML? ✅

# 6. Logger working?
# Go to /login → register → check MySQL
SELECT COUNT(*) FROM auth_logs;
# > 0? ✅
```

---

## 🎯 If Everything Fails: Reset

```bash
# 1. Stop both servers (Ctrl+C in both terminals)

# 2. Delete database
mysql -u root -p
DROP DATABASE ap_welfare_logs;
EXIT;

# 3. Reinstall and setup
cd server
rm -rf node_modules
npm install
npm run setup-db

# 4. Start servers again
npm run dev  # in server folder (terminal 1)
npm run dev  # in root folder (terminal 2)

# 5. Test again
# Visit http://localhost:3001/dashboard
# Register a new user
```

---

**Remember**: Most issues are one of these:
1. ❌ MySQL not running → `mysql -u root -p`
2. ❌ Wrong password → Check `server/.env`
3. ❌ Backend not running → Check terminal 1
4. ❌ Frontend not running → Check terminal 2
5. ❌ Wrong CORS/API URL → Check `.env.local`

**You got this!** 🎉
