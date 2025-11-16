# Render Deployment - Step by Step Guide 🚀

## ✅ Pre-Deployment Checklist

- [x] Code pushed to GitHub
- [x] All fixes applied
- [x] Dependencies updated
- [x] Environment variables ready

## 📋 Step-by-Step Deployment

### Step 1: Render Dashboard Mein Jao

1. Browser mein jao: https://dashboard.render.com
2. Login karo apne account se

### Step 2: New Web Service Create Karein (Agar pehle se nahi hai)

1. **"New +"** button click karo
2. **"Web Service"** select karo
3. **"Connect a repository"** click karo
4. Apna GitHub repository select karo: `tayyabriaz60/Deployment`
5. **"Connect"** click karo

### Step 3: Service Configuration

**Name:**
```
medical-feedback-platform
```

**Region:**
```
Singapore (ya apne location ke hisaab se)
```

**Branch:**
```
main
```

**Root Directory:**
```
(Leave empty - root se hi deploy hoga)
```

**Runtime:**
```
Python 3
```

**Build Command:**
```
pip install --upgrade pip && pip install -r requirements.txt
```

**Start Command:**
```
uvicorn app.main:asgi_app --host 0.0.0.0 --port $PORT
```

### Step 4: Environment Variables Add Karein

**"Advanced"** section mein jao aur ye variables add karo:

#### Required Variables:

1. **SECRET_KEY**
   - Key: `SECRET_KEY`
   - Value: Generate karo PowerShell mein:
     ```powershell
     python -c "import secrets; print(secrets.token_urlsafe(64))"
     ```
   - Copy output aur paste karo

2. **DATABASE_URL**
   - Key: `DATABASE_URL`
   - Value: Render database ka connection string
   - Format: `postgresql://user:password@host:port/database`
   - **Note:** Code automatically convert karega `postgresql+asyncpg://` mein

3. **GOOGLE_API_KEY**
   - Key: `GOOGLE_API_KEY`
   - Value: Apna Google Gemini API key

4. **ADMIN_EMAIL**
   - Key: `ADMIN_EMAIL`
   - Value: `aadmin@example.com`

5. **ADMIN_PASSWORD**
   - Key: `ADMIN_PASSWORD`
   - Value: `StrongPass123`
   - **Important:** Max 72 characters

#### Optional Variables:

6. **ENVIRONMENT**
   - Key: `ENVIRONMENT`
   - Value: `production`

7. **LOG_LEVEL**
   - Key: `LOG_LEVEL`
   - Value: `INFO`

8. **SQL_ECHO**
   - Key: `SQL_ECHO`
   - Value: `false`

9. **AUTO_OPEN_BROWSER**
   - Key: `AUTO_OPEN_BROWSER`
   - Value: `0`

10. **PYTHON_VERSION**
    - Key: `PYTHON_VERSION`
    - Value: `3.11.9`

### Step 5: Database Create Karein (Agar nahi hai)

1. **"New +"** → **"PostgreSQL"** select karo
2. **Name:** `feedback-db`
3. **Database:** `feedback_db`
4. **User:** `feedback_user`
5. **Plan:** Free (ya jo chahiye)
6. **Region:** Same as web service
7. **"Create Database"** click karo

### Step 6: Database URL Link Karein

1. Database service kholo
2. **"Connections"** tab mein jao
3. **"Internal Database URL"** copy karo
4. Web service ke environment variables mein `DATABASE_URL` set karo

### Step 7: Deploy Karein

1. **"Create Web Service"** click karo
2. Build start hoga automatically
3. Logs check karo:
   - ✅ Build successful
   - ✅ Application started
   - ✅ "Admin bootstrap executed" (ya "Admin bootstrap failed" - agar fail hua to bootstrap endpoint use karo)

### Step 8: Admin User Create Karein

Agar startup logs mein "Admin bootstrap failed" dikhe, to:

1. Browser mein jao: `https://your-app.onrender.com`
2. Browser console (F12) kholo
3. Ye code run karo:

```javascript
fetch(window.location.origin + '/auth/bootstrap-admin', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' }
})
.then(r => r.json())
.then(data => {
  console.log('✅ Result:', data);
  alert(JSON.stringify(data, null, 2));
})
.catch(err => {
  console.error('❌ Error:', err);
  alert('Error: ' + err.message);
});
```

4. Success message check karo

### Step 9: Login Test Karein

1. Browser mein jao: `https://your-app.onrender.com/staff`
2. Login karo:
   - **Email:** `aadmin@example.com`
   - **Password:** `StrongPass123`
3. Success! ✅

## 🔍 Troubleshooting

### Build Fails?

1. **Python Version Issue:**
   - Environment variable `PYTHON_VERSION` = `3.11.9` set karo
   - Ya `runtime.txt` file add karo with `3.11.9`

2. **Dependencies Issue:**
   - `requirements.txt` check karo
   - All packages listed hain?

3. **Database Connection:**
   - `DATABASE_URL` correct hai?
   - Database service running hai?

### Admin User Not Created?

1. **Bootstrap Endpoint Use Karo:**
   - `/auth/bootstrap-admin` POST request
   - Environment variables set hain?

2. **Check Logs:**
   - Render logs mein error dikhega
   - Password length issue?

### Login Fails?

1. **Email Match:**
   - Environment `ADMIN_EMAIL` = `aadmin@example.com`
   - Login mein bhi `aadmin@example.com` use karo

2. **Password Match:**
   - Environment `ADMIN_PASSWORD` = `StrongPass123`
   - Login mein bhi same password

## ✅ Success Checklist

- [ ] Build successful
- [ ] Application started
- [ ] Admin user created (check logs ya bootstrap endpoint)
- [ ] Login successful
- [ ] Dashboard accessible
- [ ] Feedback submit working

## 🎯 Quick Reference

**App URL:** `https://your-app.onrender.com`

**Staff Login:** `https://your-app.onrender.com/staff`

**API Docs:** `https://your-app.onrender.com/docs`

**Bootstrap Endpoint:** `POST https://your-app.onrender.com/auth/bootstrap-admin`

**Login Credentials:**
- Email: `aadmin@example.com`
- Password: `StrongPass123`

## 📞 Need Help?

1. Render logs check karo
2. Browser console (F12) check karo
3. Environment variables verify karo
4. Bootstrap endpoint try karo

Good luck! 🚀

