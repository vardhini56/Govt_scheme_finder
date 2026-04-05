# Render Deployment Guide - AP Welfare Backend

## Prerequisites
- GitHub account with your code pushed (✅ Already done)
- Render account (free at https://render.com)

---

## Step 1: Create Render Account & Login

1. Go to [render.com](https://render.com)
2. Sign up with GitHub
3. Authorize Render to access your GitHub repositories
4. Dashboard will open

---

## Step 2: Create MySQL Database

1. Click **"+ New"** button
2. Select **"MySQL"**
3. Fill in the details:
   - **Name**: `ap-welfare-mysql`
   - **Database**: `ap_welfare_logs`
   - **Username**: `root`
   - **Password**: Create a strong password (save it!)
   - **Plan**: Free
4. Click **"Create Database"**
5. **SAVE these credentials** - you'll need them for the web service

---

## Step 3: Create Web Service

1. Click **"+ New"** → **"Web Service"**
2. Select your GitHub repository: `SatyaPrakash0817/government`

3. **Build Configuration**:
   - **Name**: `ap-welfare-backend`
   - **Environment**: `Node`
   - **Build Command**: `npm install`
   - **Start Command**: `npm start`
   - **Plan**: Free

4. Click **"Create Web Service"**

---

## Step 4: Add Environment Variables

After the web service is created:

1. Go to the service dashboard
2. Click **"Environment"** tab
3. Click **"Add Environment Variable"** for each:

| Variable | Value |
|----------|-------|
| `NODE_ENV` | `production` |
| `SERVER_PORT` | `3001` |
| `MYSQL_HOST` | `[YOUR-MYSQL-INTERNAL-IP]` |
| `MYSQL_USER` | `root` |
| `MYSQL_PASSWORD` | `[PASSWORD-YOU-SET]` |
| `MYSQL_DATABASE` | `ap_welfare_logs` |
| `CORS_ORIGIN` | `http://localhost:8080` |

### How to get MYSQL_HOST:
1. Go to your MySQL service dashboard
2. Copy the **Internal Database URL** (it looks like `dpg-xxx.render.com`)
3. Extract just the hostname part

---

## Step 5: Configure Network Connection

1. In your MySQL service details, find **"Connections"**
2. Copy the **Internal Database URL**
3. In your Web Service environment, set:
   - `MYSQL_HOST`: The hostname from the internal URL

---

## Step 6: Deploy

1. Return to your Web Service dashboard
2. Render will automatically start deploying
3. Watch the **"Logs"** tab for build progress
4. Once "Build successful" appears, deployment is complete

---

## Step 7: Get Your Backend URL

1. In your Web Service dashboard, find the **URL** at the top
2. It will look like: `https://ap-welfare-backend.onrender.com`
3. **Copy this URL** - you'll need it for the frontend

---

## Step 8: Verify Backend is Working

Open this in your browser:
```
https://[YOUR-BACKEND-URL]/health
```

**Expected response**:
```json
{
  "status": "healthy",
  "database": "connected"
}
```

---

## Important Notes

### Free Plan Limitations
- Services spin down after 15 mins of inactivity
- Will take ~30 seconds to wake up on first request
- No downtime during activity

### To Upgrade
If you need production-grade reliability:
1. Go to service settings
2. Click **"Instance Type"**
3. Upgrade to paid plan

### Auto-Deploy from GitHub
Render automatically redeploys when you push to GitHub:
```bash
git add .
git commit -m "Update backend"
git push origin main
```

The service will automatically rebuild and deploy!

---

## Troubleshooting

### Database Connection Error
- Verify `MYSQL_HOST`, `MYSQL_USER`, `MYSQL_PASSWORD` are correct
- Check MySQL service is running (green status)
- Make sure `MYSQL_DATABASE` is `ap_welfare_logs`

### Build Fails
- Check **Logs** tab for error details
- Ensure `server/package.json` has `npm start` command
- Verify `server/src/server.js` exists

### Timeout Errors
- Free tier services may be slow to start
- Wait 30-60 seconds for service to wake up
- Consider upgrading to paid plan for production

### CORS Errors
- Update `CORS_ORIGIN` after deploying frontend
- Should match exactly: `https://your-vercel-domain.vercel.app`

---

## Environment Variables Reference

```env
NODE_ENV=production
SERVER_PORT=3001
MYSQL_HOST=dpg-xxx.render.com
MYSQL_USER=root
MYSQL_PASSWORD=your_secure_password
MYSQL_DATABASE=ap_welfare_logs
CORS_ORIGIN=https://your-frontend-domain.com
```

---

## Next Steps

1. ✅ Backend deployed to Render
2. Note your backend URL: `https://ap-welfare-backend.onrender.com`
3. Deploy frontend to Vercel
4. Update `CORS_ORIGIN` after frontend deployment
5. Connect frontend to backend API URL

**Questions?** Check the Logs tab in Render for detailed error messages!
