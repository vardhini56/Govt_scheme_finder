# Debugging Guide - Login/Registration Not Working

## Step 1: Check Browser Console for Errors

1. Open your Vercel frontend URL on your computer
2. Press **F12** to open Developer Tools
3. Go to **Console** tab
4. Try to Register/Login
5. Look for any error messages (red text)

### Common Error Messages:

**Error**: `Cannot POST https://ap-welfare-backend.onrender.com/api/auth/register`
→ Backend URL is wrong OR backend is not running

**Error**: `CORS error` / `Access-Control-Allow-Origin`
→ CORS not configured properly in Render backend

**Error**: `Network Error` / `Failed to fetch`
→ Check your internet connection or backend is down

**Error**: `VITE_API_URL is undefined`
→ Environment variable not set in Vercel

---

## Step 2: Verify Environment Variables

### On Vercel:

1. Go to [vercel.com](https://vercel.com)
2. Select **government** project
3. Click **Settings** → **Environment Variables**
4. **You should see:**
   ```
   VITE_API_URL = https://ap-welfare-backend.onrender.com/api
   ```

❌ **If Missing or Wrong:**
- Click **Add New**
- Name: `VITE_API_URL`
- Value: `https://ap-welfare-backend.onrender.com/api`  
- Environments: Check all three (Production, Preview, Development)
- Click **Save**
- Go to **Deployments** → Click latest → **Redeploy**

---

## Step 3: Verify Backend is Running

Test your backend directly:

1. Open in browser:
   ```
   https://ap-welfare-backend.onrender.com/health
   ```

2. **Expected response:**
   ```json
   {"status":"healthy","database":"connected"}
   ```

❌ **If you get error:**
- Go to [render.com](https://render.com)
- Select **ap-welfare-backend** service
- Check **Logs** tab for errors
- Service status should be green (Running)

---

## Step 4: Verify CORS Configuration

On Render backend:

1. Go to [render.com](https://render.com)
2. Select **ap-welfare-backend** service
3. Click **Environment** tab
4. Find `CORS_ORIGIN` variable
5. **It should be:**
   ```
   https://government-es42-me1yic399-satyaprakash0817s-projects.vercel.app
   ```

❌ **If Wrong or Missing:**
- Update it to your exact Vercel URL above
- Click **Save**
- Click **Redeploy** button
- Wait 2-3 minutes for redeploy to complete

---

## Step 5: Verify MySQLDatabase

On Render backend logs, check for database errors:

1. Go to [render.com](https://render.com)
2. Select **ap-welfare-backend** service
3. Click **Logs** tab
4. Look for messages like:
   - ✅ `Database initialized successfully` - GOOD
   - ❌ `Cannot connect to database` - PROBLEM

If database error:
- Check **Environment Variables** for MySQL credentials
- These should auto-populate from the MySQL service
- Required variables:
  - `MYSQL_HOST`
  - `MYSQL_USER` (usually `root`)
  - `MYSQL_PASSWORD`
  - `MYSQL_DATABASE` (should be `ap_welfare_logs`)

---

## Step 6: Test API Endpoint Directly

Use browser or Postman to test:

```
POST https://ap-welfare-backend.onrender.com/api/auth/register
Content-Type: application/json

{
  "name": "Test User",
  "email": "test@example.com",
  "phone": "1234567890",
  "password": "TestPassword123"
}
```

**Expected response:**
```json
{
  "success": true,
  "message": "User registered successfully",
  "user": {
    "id": "xxx",
    "name": "Test User",
    "email": "test@example.com"
  }
}
```

❌ **If error:**
- Check `/api` path is correct
- Check spelling of `/auth/register`
- Check backend is actually running

---

## Step 7: Verify Frontend is Using Correct URL

1. In Vercel frontend, press F12 → **Network** tab
2. Try to Register
3. Look for request to:
   ```
   POST https://ap-welfare-backend.onrender.com/api/auth/register
   ```

❌ **If request goes to localhost:**
- Environment variable not loaded
- Redeploy Vercel again
- Clear browser cache (Ctrl+Shift+Delete)
- Close and reopen browser

---

## Complete Checklist

- [ ] Browser console shows no errors (F12 → Console)
- [ ] Backend health check returns healthy (test `/health`)
- [ ] VITE_API_URL is set in Vercel Environment Variables
- [ ] CORS_ORIGIN in Render matches Vercel URL exactly
- [ ] Render backend service status is "Running" (green)
- [ ] MySQL service is "Running" (green)
- [ ] Backend logs show "Database initialized successfully"
- [ ] Network requests go to correct backend URL (not localhost)
- [ ] Vercel has been redeployed after env changes

---

## Quick Fix Summary

If nothing works, follow this order:

1. **Redeploy Vercel**
   - Settings → Environment Variables → Verify VITE_API_URL
   - Deployments → Latest → Redeploy

2. **Redeploy Render Backend**
   - Environment → Verify CORS_ORIGIN matches your Vercel URL
   - Redeploy

3. **Clear Browser Cache**
   - Ctrl+Shift+Delete
   - Check "Cached images and files"
   - Clear

4. **Test Health Check**
   - Visit: `https://ap-welfare-backend.onrender.com/health`
   - Should show `{"status":"healthy","database":"connected"}`

5. **Check Console Errors**
   - F12 → Console → Register → Look for red errors

---

## Still Not Working?

Get the exact error message from browser console (F12):
- Take a screenshot
- Note the exact error text
- Share it for specific debugging
