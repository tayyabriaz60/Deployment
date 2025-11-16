# ⚡ Render Quick Start - 5 Minutes

## 🚀 Fastest Way to Deploy

### Step 1: GitHub Push
```bash
git add .
git commit -m "Ready for Render"
git push origin main
```

### Step 2: Render Setup

1. **Go to**: https://dashboard.render.com
2. **Click**: "New +" → "Web Service"
3. **Connect**: Your GitHub repository
4. **Configure**:
   - **Name**: `medical-feedback-platform`
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `uvicorn app.main:asgi_app --host 0.0.0.0 --port $PORT`

### Step 3: Create Database

1. **Click**: "New +" → "PostgreSQL"
2. **Name**: `feedback-db`
3. **Copy**: Internal Database URL

### Step 4: Environment Variables

Add these in Web Service → Environment:

```
SECRET_KEY=<generate with: python -c "import secrets; print(secrets.token_urlsafe(64))">
GOOGLE_API_KEY=<your-gemini-api-key>
DATABASE_URL=<postgresql+asyncpg://user:pass@host:port/db>
ENVIRONMENT=production
LOG_LEVEL=INFO
SQL_ECHO=false
AUTO_OPEN_BROWSER=0
ADMIN_EMAIL=admin@example.com
ADMIN_PASSWORD=StrongPass123!@#
```

### Step 5: Deploy

Click "Deploy" and wait 5-10 minutes!

---

## ✅ Done!

Your app will be live at: `https://your-app-name.onrender.com`

**Full Guide**: See `RENDER_DEPLOYMENT_GUIDE.md` for detailed steps.

