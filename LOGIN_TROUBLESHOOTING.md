# Login Failed - Troubleshooting Guide

## Quick Checks

### 1. Check Render Logs

Render dashboard → Web Service → Logs

Look for these messages:

**✅ Admin User Created:**
```
INFO - Creating initial admin user: admin@example.com
INFO - Admin user created successfully: admin@example.com (ID: 1)
INFO - Admin bootstrap executed
```

**❌ Admin User NOT Created:**
```
ERROR - Admin bootstrap failed: password cannot be longer than 72 bytes
```
OR
```
ERROR - Failed to create admin user: ...
```

### 2. Check Render Environment Variables

Render Dashboard → Web Service → Environment

Verify these are set:
- `ADMIN_EMAIL` = `admin@example.com` (ya jo email use kiya)
- `ADMIN_PASSWORD` = `StrongPass123` (max 72 characters)

### 3. Check Login Attempts in Logs

When you try to login, look for:

**User Not Found:**
```
WARNING - Login failed: User not found for email: admin@example.com
```

**Invalid Password:**
```
WARNING - Login failed: Invalid password for email: admin@example.com
```

**Success:**
```
INFO - Login successful for user: admin@example.com (role: admin)
```

## Common Issues & Solutions

### Issue 1: Admin User Not Created

**Problem:** Admin bootstrap failed during startup

**Solution:**
1. Check Render logs for bootstrap error
2. If password too long (>72 chars), shorten it in Render environment
3. Redeploy the service
4. Check logs again to confirm admin user created

### Issue 2: User Not Found

**Problem:** Login says "Invalid credentials" but user doesn't exist

**Solution:**
1. Check if admin user was created:
   - Look for "Admin user created successfully" in logs
   - If not, admin bootstrap failed
2. Fix the bootstrap issue (usually password length)
3. Redeploy to create admin user
4. Try login again

### Issue 3: Invalid Password

**Problem:** User exists but password doesn't match

**Possible Causes:**
1. Password in Render environment is different from what you're typing
2. Password was truncated during creation (if >72 chars)
3. Special characters in password causing issues

**Solution:**
1. Verify exact password in Render environment variables
2. Use simple password (letters + numbers, <72 chars)
3. Try login with exact password from environment

### Issue 4: Password Truncation

**Problem:** Password >72 characters, gets truncated

**What Happens:**
- Original: `VeryLongPassword123456789012345678901234567890...` (80 chars)
- Truncated: First 72 bytes only
- Login: Must use first 72 characters

**Solution:**
- Use password ≤72 characters (recommended)
- Or remember only first 72 characters work

## Step-by-Step Fix

### Step 1: Verify Environment Variables

1. Go to Render Dashboard
2. Select your Web Service
3. Go to "Environment" tab
4. Check:
   - `ADMIN_EMAIL` exists and is correct
   - `ADMIN_PASSWORD` exists and is ≤72 characters

### Step 2: Check Startup Logs

1. Go to "Logs" tab
2. Scroll to application startup
3. Look for:
   - `Admin bootstrap executed` ✅
   - OR `Admin bootstrap failed` ❌

### Step 3: If Bootstrap Failed

1. Fix the issue (usually password length)
2. Update environment variable
3. Click "Manual Deploy" → "Deploy latest commit"
4. Wait for deployment
5. Check logs again

### Step 4: Test Login

1. Go to: `https://your-app.onrender.com/staff`
2. Enter:
   - Email: Same as `ADMIN_EMAIL` in Render
   - Password: Same as `ADMIN_PASSWORD` in Render (first 72 chars if longer)
3. Click Login
4. Check browser console (F12) for errors
5. Check Render logs for login attempt

## Manual Admin Creation (If Needed)

If admin user still not created, you can create it via API:

### Option 1: Using API Docs

1. Go to: `https://your-app.onrender.com/docs`
2. Find `/auth/register` endpoint
3. Use this payload:
```json
{
  "email": "admin@example.com",
  "password": "StrongPass123",
  "role": "admin"
}
```
4. Click "Try it out" → "Execute"

**Note:** This only works if NO users exist in database.

### Option 2: Using curl

```bash
curl -X POST "https://your-app.onrender.com/auth/register" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "admin@example.com",
    "password": "StrongPass123",
    "role": "admin"
  }'
```

## Recommended Password

Use a simple, strong password:

✅ **Good:**
- `AdminPass123!@#` (15 chars)
- `SecurePassword2024!` (19 chars)
- `MyAdmin123!` (11 chars)

❌ **Avoid:**
- Passwords >72 characters
- Very complex passwords with many special chars (might cause encoding issues)

## Still Having Issues?

1. Check Render logs for exact error messages
2. Verify environment variables are set correctly
3. Ensure password is ≤72 characters
4. Try redeploying the service
5. Check browser console (F12) for frontend errors

## Expected Login Flow

1. User visits `/staff`
2. Enters email and password
3. Frontend sends POST to `/auth/login`
4. Backend checks:
   - User exists? ✅
   - Password matches? ✅
5. Returns JWT token
6. Frontend stores token in localStorage
7. Redirects to `/` (dashboard)

## Debug Checklist

- [ ] `ADMIN_EMAIL` set in Render environment
- [ ] `ADMIN_PASSWORD` set in Render environment
- [ ] Password ≤72 characters
- [ ] Admin user created (check logs)
- [ ] Using correct email for login
- [ ] Using correct password for login
- [ ] No CORS errors in browser console
- [ ] API endpoint accessible (`/auth/login`)

