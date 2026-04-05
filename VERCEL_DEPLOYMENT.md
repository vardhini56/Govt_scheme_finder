# Vercel Deployment Guide - AP Welfare Frontend

## Prerequisites
- GitHub account with your code pushed (✅ Already done)
- Vercel account (free at https://vercel.com)
- Your Render backend URL (get from Render dashboard)

---

## Step 1: Get Your Backend URL from Render

1. Go to [render.com](https://render.com) dashboard
2. Select your **ap-welfare-backend** service
3. Copy the **URL** at the top (e.g., `https://ap-welfare-backend-xxxx.onrender.com`)
4. **SAVE THIS** - you'll need it for Vercel

---

## Step 2: Create Vercel Account

1. Go to [vercel.com](https://vercel.com)
2. Click **"Sign Up"**
3. Choose **"Continue with GitHub"**
4. Authorize Vercel to access your GitHub repositories

---

## Step 3: Import Repository

1. In Vercel dashboard, click **"Add New..."** → **"Project"**
2. Click **"Import Git Repository"**
3. Find and select: `SatyaPrakash0817/government`
4. Click **"Import"**

---

## Step 4: Configure Project

1. **Framework Preset**: Vercel will auto-detect **Vite** ✅
2. **Build Command**: `npm run build` (should be auto-filled)
3. **Output Directory**: `dist` (should be auto-filled)
4. **Install Command**: `npm install` (should be auto-filled)

---

## Step 5: Add Environment Variables

Before deploying, you MUST add your backend URL:

1. In the import dialog, scroll to **"Environment Variables"**
2. Click **"Add"** and fill in:
   
   | Name | Value |
   |------|-------|
   | `VITE_API_URL` | `https://your-render-backend-url.onrender.com/api` |

   **Example**: `https://ap-welfare-backend-xyz.onrender.com/api`

3. Click **"Deploy"**

---

## Step 6: Wait for Deployment

Vercel will:
1. Clone your repository
2. Install dependencies
3. Build the frontend (`npm run build`)
4. Deploy to CDN

Watch the build logs - should complete in 2-3 minutes.

---

## Step 7: Get Your Frontend URL

Once deployment succeeds:
1. You'll see your Vercel project dashboard
2. Your **Production URL** is displayed prominently
3. It looks like: `https://government-xxxx.vercel.app`
4. **SAVE THIS** - you'll need it for backend CORS

**Click the URL to open your app!** ✅

---

## Step 8: Update Backend CORS (Important!)

Now update your backend to accept requests from Vercel:

1. Go to [render.com](https://render.com) dashboard
2. Select your **ap-welfare-backend** service
3. Go to **"Environment"** tab
4. Find the `CORS_ORIGIN` variable
5. Update it to your Vercel URL:
   ```
   https://your-vercel-domain.vercel.app
   ```
   **Example**: `https://government-xyz.vercel.app`

6. Click **"Redeploy"** to apply changes

---

## Step 9: Test Everything

1. Open your Vercel frontend URL
2. Try to **Register/Login**
3. Should connect to your Render backend successfully ✅

---

## Useful Vercel Commands

### View Logs
- Dashboard → select your project → **"Deployments"** → click a deployment → **"Logs"**

### Redeploy Latest Commit
- Dashboard → select your project → **"Deployments"** → click **"..."** → **"Redeploy"**

### Environment Variables
- Dashboard → select your project → **"Settings"** → **"Environment Variables"**

---

## Troubleshooting

### Build Fails
- Check **Build Logs** in Vercel dashboard
- Ensure all dependencies are in `package.json`
- Check for TypeScript errors: `npm run build` locally

### Frontend doesn't connect to backend
- Verify `CORS_ORIGIN` in Render matches your Vercel URL
- Check Network tab in browser DevTools
- Verify `VITE_API_URL` environment variable is set in Vercel
- Check Render backend is running (`/health` endpoint)

### Page not found after deployment
- Vercel should handle SPA routing automatically
- If not, you might need `vercel.json` (already created)

### Slow performance
- Vercel caches builds - watch Build Logs
- Check Response time in Browser DevTools
- Consider upgrading Vercel plan for production

---

## Environment Variable Reference

```
VITE_API_URL=https://ap-welfare-backend-xxxx.onrender.com/api
```

This tells your frontend where to send API requests.

---

## Final Checklist

- ✅ Backend deployed on Render
- ✅ Frontend deployed on Vercel  
- ✅ CORS_ORIGIN updated to Vercel domain
- ✅ VITE_API_URL set to Render backend URL
- ✅ Tested register/login flow

---

## Next Steps

1. Test your live application
2. Share the Vercel URL with users
3. Monitor logs for any issues
4. Update CORS_ORIGIN if frontend URL changes

**Your app is now LIVE! 🎉**
