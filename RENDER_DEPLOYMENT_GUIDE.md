# 🚀 Render Deployment Guide - Step by Step

## 📋 Prerequisites

1. **Render Account**: Sign up at [render.com](https://render.com) (free tier available)
2. **GitHub Account**: Code ko GitHub repository mein push karna hoga
3. **Google Gemini API Key**: [Google AI Studio](https://makersuite.google.com/app/apikey) se API key lena hoga

---

## 📝 Step 1: GitHub Repository Setup

### 1.1 GitHub Repository Create Karein

```bash
# Agar abhi tak GitHub repo nahi hai:
cd "C:\Users\tayyab\Desktop\Software-house\Deployment\Git-Second-Testing\Medical-Feedback-Analysis-Platform-"

# Git initialize (agar nahi hai)
git init

# .gitignore check karein (already hai)
# .env file ko verify karein ke gitignore mein hai

# Files add karein
git add .

# Commit karein
git commit -m "Initial commit - Ready for Render deployment"

# GitHub par new repository create karein
# Phir remote add karein:
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
git branch -M main
git push -u origin main
```

**Important**: `.env` file ko **kabhi bhi commit mat karna** - wo already `.gitignore` mein hai.

---

## 📝 Step 2: Render Dashboard Setup

### 2.1 Render Account Create/Login

1. [render.com](https://render.com) par jao
2. "Get Started for Free" click karo
3. GitHub account se sign in karo (recommended)

### 2.2 New Web Service Create Karein

1. Dashboard mein **"New +"** button click karo
2. **"Web Service"** select karo
3. Apni GitHub repository select karo
4. **"Connect"** click karo

---

## 📝 Step 3: Web Service Configuration

### 3.1 Basic Settings

**Name**: `medical-feedback-platform` (ya koi aur naam)

**Region**: Apne location ke hisaab se select karo (Singapore, US, etc.)

**Branch**: `main` (ya jo branch use kar rahe ho)

**Root Directory**: (empty chhodo - root se hi run hoga)

**Runtime**: `Python 3`

**Build Command**: 
```bash
pip install -r requirements.txt
```

**Start Command**: 
```bash
uvicorn app.main:asgi_app --host 0.0.0.0 --port $PORT
```

**Note**: Render automatically `$PORT` environment variable set karta hai.

---

## 📝 Step 4: PostgreSQL Database Create Karein

### 4.1 Database Service Add Karein

1. Dashboard mein **"New +"** button click karo
2. **"PostgreSQL"** select karo
3. Settings configure karo:
   - **Name**: `feedback-db`
   - **Database**: `feedback_db`
   - **User**: `feedback_user` (ya default)
   - **Region**: Web service ke same region select karo
   - **Plan**: Free tier (ya paid agar chahiye)
4. **"Create Database"** click karo

### 4.2 Database Connection String Copy Karein

1. Database service open karo
2. **"Connections"** tab mein jao
3. **"Internal Database URL"** copy karo
   - Format: `postgresql://user:password@host:port/database`
   - Isko `DATABASE_URL` mein use karenge

**Important**: Internal URL use karein (same region mein fast connection ke liye)

---

## 📝 Step 5: Environment Variables Setup

### 5.1 Web Service mein Environment Variables Add Karein

Web service settings mein **"Environment"** section mein jao aur ye variables add karo:

#### Required Variables:

1. **SECRET_KEY**
   ```
   Value: (Generate karein - see below)
   ```
   Generate karne ke liye:
   ```bash
   python -c "import secrets; print(secrets.token_urlsafe(64))"
   ```
   Output ko copy karke SECRET_KEY mein paste karo.

2. **GOOGLE_API_KEY**
   ```
   Value: (Apna Google Gemini API key)
   ```
   [Google AI Studio](https://makersuite.google.com/app/apikey) se API key lein.

3. **DATABASE_URL**
   ```
   Value: (PostgreSQL database ka internal URL)
   ```
   Step 4.2 se copy kiya hua database URL paste karo.
   
   **Important**: Agar Render ka PostgreSQL use kar rahe ho, to format change karna hoga:
   ```
   Original: postgresql://user:password@host:port/database
   Convert to: postgresql+asyncpg://user:password@host:port/database
   ```
   Ya phir direct Render ka internal URL use karo (asyncpg automatically handle karega).

4. **ENVIRONMENT**
   ```
   Value: production
   ```

5. **LOG_LEVEL**
   ```
   Value: INFO
   ```

6. **SQL_ECHO**
   ```
   Value: false
   ```

7. **AUTO_OPEN_BROWSER**
   ```
   Value: 0
   ```

#### Optional Variables (with defaults):

8. **ADMIN_EMAIL**
   ```
   Value: admin@yourdomain.com
   ```
   (First admin user automatically create hoga)

9. **ADMIN_PASSWORD**
   ```
   Value: StrongPassword123!@#
   ```
   (Minimum 12 characters, mixed case, numbers, symbols)

10. **DB_POOL_SIZE**
    ```
    Value: 20
    ```

11. **DB_MAX_OVERFLOW**
    ```
    Value: 10
    ```

12. **DB_POOL_TIMEOUT**
    ```
    Value: 30
    ```

13. **DB_POOL_RECYCLE**
    ```
    Value: 3600
    ```

14. **GEMINI_MODEL**
    ```
    Value: gemini-2.5-flash
    ```
    (Ya jo model use karna ho)

15. **GEMINI_TIMEOUT_SECONDS**
    ```
    Value: 30
    ```

### 5.2 Environment Variables Add Karne ka Method

1. Web service settings mein **"Environment"** section open karo
2. **"Add Environment Variable"** click karo
3. **Key** aur **Value** enter karo
4. Har variable ke liye repeat karo
5. **"Save Changes"** click karo

---

## 📝 Step 6: Build aur Deploy

### 6.1 Manual Deploy

1. Environment variables save karne ke baad
2. **"Manual Deploy"** section mein jao
3. **"Deploy latest commit"** click karo
4. Build process start hoga

### 6.2 Build Process Monitor Karein

1. **"Logs"** tab open karo
2. Build logs dekh sakte ho:
   - Dependencies install honge
   - Application start hoga
   - Errors agar honge to dikhenge

### 6.3 Common Build Issues aur Solutions

**Issue 1**: `Module not found`
- **Solution**: `requirements.txt` check karo, sab dependencies listed hain

**Issue 2**: `Port already in use`
- **Solution**: Start command mein `$PORT` use karo (Render automatically set karta hai)

**Issue 3**: `Database connection failed`
- **Solution**: 
  - DATABASE_URL check karo
  - Database service running hai verify karo
  - Internal URL use kar rahe ho (external nahi)

**Issue 4**: `SECRET_KEY not set`
- **Solution**: Environment variable properly set karo

---

## 📝 Step 7: Health Check Setup

### 7.1 Health Check Path

Render automatically health check karta hai. Apne code mein already `/health` endpoint hai.

**Settings**:
- **Health Check Path**: `/health`
- **Health Check Interval**: 10 seconds (default)

---

## 📝 Step 8: Custom Domain (Optional)

### 8.1 Custom Domain Add Karein

1. Web service settings mein **"Custom Domains"** section
2. **"Add Custom Domain"** click karo
3. Apna domain enter karo
4. DNS settings follow karo (Render instructions dega)

---

## 📝 Step 9: Post-Deployment Verification

### 9.1 Application Test Karein

1. **Render URL** copy karo (format: `https://your-app-name.onrender.com`)
2. Browser mein open karo
3. Test karo:
   - ✅ Homepage load ho raha hai?
   - ✅ `/health` endpoint working hai?
   - ✅ `/docs` (API documentation) accessible hai?
   - ✅ Feedback submit kar sakte ho?
   - ✅ Staff login working hai?

### 9.2 Logs Check Karein

1. Web service dashboard mein **"Logs"** tab
2. Real-time logs dekh sakte ho
3. Errors agar honge to yahan dikhenge

### 9.3 Database Verify Karein

1. Database service dashboard mein **"Info"** tab
2. Connection status check karo
3. Tables automatically create honge first run par

---

## 📝 Step 10: Auto-Deploy Setup (Recommended)

### 10.1 Auto-Deploy Enable Karein

1. Web service settings mein **"Auto-Deploy"** section
2. **"Auto-Deploy"** enable karo
3. **Branch**: `main` select karo
4. Ab har commit par automatically deploy hoga

---

## 🔧 Troubleshooting

### Problem 1: Application Start Nahi Ho Raha

**Check**:
1. Logs mein error dekh lo
2. Environment variables properly set hain?
3. DATABASE_URL correct format mein hai?
4. SECRET_KEY set hai?

**Solution**:
```bash
# Logs check karo
# Common issues:
# - Missing environment variables
# - Database connection string wrong format
# - Port issue (use $PORT in start command)
```

### Problem 2: Database Connection Error

**Check**:
1. Database service running hai?
2. DATABASE_URL correct hai?
3. Internal URL use kar rahe ho (same region)?

**Solution**:
```
# DATABASE_URL format:
postgresql+asyncpg://user:password@host:port/database

# Ya Render ka internal URL directly use karo
```

### Problem 3: Build Fails

**Check**:
1. `requirements.txt` complete hai?
2. Python version compatible hai?
3. Dependencies install ho rahe hain?

**Solution**:
```bash
# Locally test karo:
pip install -r requirements.txt
# Agar locally work karta hai, to Render par bhi hoga
```

### Problem 4: Socket.IO Not Working

**Check**:
1. WebSocket support enabled hai?
2. CORS settings correct hain?

**Solution**:
- Render automatically WebSocket support karta hai
- Agar issue ho to logs check karo

---

## 📊 Render Free Tier Limitations

### Free Tier Limits:

1. **Spinning Down**: 15 minutes inactivity ke baad app sleep ho jata hai
   - **Solution**: Paid plan use karo ya external service se periodic ping karo

2. **Build Time**: 10 minutes max
   - **Solution**: Usually enough hai, agar zyada time lage to optimize karo

3. **Database**: 1 GB storage (free tier)
   - **Solution**: Sufficient hai starting ke liye

4. **Bandwidth**: Limited
   - **Solution**: Normal usage ke liye enough hai

### Paid Tier Benefits:

- No sleep/spin down
- Faster builds
- More resources
- Better support

---

## 🎯 Quick Deployment Checklist

- [ ] GitHub repository ready hai
- [ ] Code push ho chuka hai
- [ ] Render account create ho gaya
- [ ] Web service create ho gaya
- [ ] PostgreSQL database create ho gaya
- [ ] Environment variables set ho gaye (specially SECRET_KEY, GOOGLE_API_KEY, DATABASE_URL)
- [ ] Build successful hai
- [ ] Application running hai
- [ ] Health check passing hai
- [ ] Frontend accessible hai
- [ ] API working hai
- [ ] Database connection working hai

---

## 🔗 Useful Links

- **Render Dashboard**: https://dashboard.render.com
- **Render Docs**: https://render.com/docs
- **Python on Render**: https://render.com/docs/deploy-python-apps
- **PostgreSQL on Render**: https://render.com/docs/databases

---

## 📞 Support

Agar koi issue ho to:

1. **Render Logs** check karo (detailed errors milenge)
2. **Health Check** endpoint test karo: `https://your-app.onrender.com/health`
3. **Database** connection verify karo
4. **Environment Variables** double-check karo

---

## ✅ Success!

Agar sab kuch theek hai to:

- ✅ Application live hai
- ✅ Database connected hai
- ✅ Frontend accessible hai
- ✅ API working hai
- ✅ Real-time features (Socket.IO) working hain

**Congratulations! 🎉 Apka application successfully deploy ho gaya hai!**

---

## 🔄 Updates Deploy Karna

Jab bhi code update karna ho:

1. Local changes commit karo
2. GitHub par push karo
3. Auto-deploy enabled hai to automatically deploy hoga
4. Ya manually **"Manual Deploy"** se deploy karo

---

**Note**: Pehli baar deploy hone mein 5-10 minutes lag sakte hain. Patience rakho! 😊

