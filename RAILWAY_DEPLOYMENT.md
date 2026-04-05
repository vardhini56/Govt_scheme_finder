# Railway Deployment Guide - AP Welfare Backend

## Step 1: Prepare for Deployment

Make sure all files are committed to Git:
```bash
git add .
git commit -m "Prepare for Railway deployment"
git push origin main
```

## Step 2: Create Railway Project

1. Go to [railway.app](https://railway.app)
2. Click **"New Project"**
3. Select **"Deploy from GitHub repo"**
4. Authorize Railway to access your GitHub account
5. Select your repository: `government_2`

## Step 3: Configure Railway Project

After selecting your repo, Railway will detect the Node.js backend:

1. **Select the root directory**: When prompted, choose the `server` folder as your deployment directory
2. Click **"Deploy Now"**

## Step 4: Add Environment Variables

1. In your Railway project, click **"Variables"**
2. Add the following environment variables:

   | Variable | Value | Notes |
   |----------|-------|-------|
   | `NODE_ENV` | `production` | |
   | `SERVER_PORT` | `$PORT` | Railway automatically assigns PORT |
   | `MYSQL_HOST` | `your-mysql-host` | Get from Railway MySQL plugin |
   | `MYSQL_USER` | `root` | Default Railway MySQL user |
   | `MYSQL_PASSWORD` | `your-password` | Set a secure password |
   | `MYSQL_DATABASE` | `ap_welfare_logs` | Database name |
   | `CORS_ORIGIN` | `https://your-app.vercel.app` | Your Vercel frontend URL (add later) |

### To Add MySQL Plugin:

1. In Railway dashboard, click **"+ Add"**
2. Select **"Add from Marketplace"**
3. Search for **"MySQL"**
4. Click **"Add MySQL"**
5. MySQL will automatically populate connection variables

## Step 5: Deploy

1. Click **"Deploy"** button
2. Wait for build to complete (2-5 minutes)
3. Once deployment succeeds, copy your Railway URL:
   - Go to your backend service
   - Copy the **Public Domain** URL (e.g., `https://your-app-production.up.railway.app`)

## Step 6: Update CORS for Frontend

After deploying frontend to Vercel:

1. In Railway dashboard, go to **Variables**
2. Update `CORS_ORIGIN` to your Vercel domain
3. Click **"Redeploy"**

## Testing Your Deployment

Test the API health check:
```bash
curl https://your-railway-url.up.railway.app/health
```

Expected response:
```json
{
  "status": "healthy",
  "database": "connected"
}
```

## Troubleshooting

### Build Fails
- Check that `server/package.json` has the `start` script
- View logs in Railway dashboard under **Logs**

### Database Connection Error
- Verify MySQL environment variables are correct
- Check that MySQL plugin is running
- Ensure MYSQL_PASSWORD matches what you set

### CORS Errors
- Update `CORS_ORIGIN` in environment variables
- Should match exactly: `https://your-vercel-domain.vercel.app`

### Port Issues
- Don't hard-code port; use `process.env.PORT` or `process.env.SERVER_PORT`
- Railway automatically assigns a PUBLIC_URL and PORT

## After Deployment

1. **Get Backend URL**: Copy from Railway public domain
2. **Update Frontend**: Set `VITE_API_URL` in Vercel to this URL
3. **Redeploy Frontend**: So it uses the correct API endpoint

## Continuous Deployment

Railway will automatically redeploy when you push to your main branch (if you connected to GitHub).

To disable: Go to Railway settings and disconnect from GitHub, or use Railway CLI for manual deployments.
