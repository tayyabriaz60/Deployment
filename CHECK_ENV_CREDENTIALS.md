# .env File - Staff/Admin Credentials Check

## Required Staff/Admin Credentials

Apne `.env` file mein ye **2 variables** zaroor honi chahiye:

```env
ADMIN_EMAIL=admin@example.com
ADMIN_PASSWORD=YourStrongPassword123!@#
```

## Kaise Check Karein

### Option 1: Manual Check
1. `.env` file kholo
2. Search karo: `ADMIN_EMAIL` aur `ADMIN_PASSWORD`
3. Dono set honi chahiye

### Option 2: PowerShell Check
```powershell
# Check if variables exist
Select-String -Path .env -Pattern "ADMIN_EMAIL|ADMIN_PASSWORD"
```

## Important Points

### ADMIN_EMAIL
- ✅ Valid email format honi chahiye
- ✅ Example: `admin@hospital.com` ya `admin@example.com`
- ❌ Empty nahi honi chahiye

### ADMIN_PASSWORD
- ✅ **Maximum 72 characters** (bcrypt limit)
- ✅ Strong password (letters, numbers, symbols)
- ✅ Example: `AdminPass123!@#` (15 chars - safe)
- ❌ 72+ characters nahi honi chahiye

## Render Deployment Ke Liye

Render dashboard mein bhi ye variables set karni hongi:

1. **Render Dashboard** → **Web Service** → **Environment**
2. Add these variables:
   - `ADMIN_EMAIL` = `admin@yourdomain.com`
   - `ADMIN_PASSWORD` = `StrongPassword123!@#` (max 72 chars)

## Password Length Warning

Agar password 72+ characters hai:
- ❌ Error: `password cannot be longer than 72 bytes`
- ✅ Solution: Password ko 72 characters tak reduce karo
- ✅ Ya code automatically truncate kar dega (first 72 bytes use honge)

## Quick Test

Agar `.env` file sahi hai, to application start hone par ye log dikhna chahiye:

```
INFO - Admin bootstrap executed
```

Agar ye dikhe:
```
ERROR - Admin bootstrap failed: password cannot be longer than 72 bytes
```

To password ko shorten karo.

## Example .env File Structure

```env
# Security (required)
SECRET_KEY=your-secret-key-here
GOOGLE_API_KEY=your-google-api-key

# Database
DATABASE_URL=postgresql+asyncpg://user:pass@host/db

# Staff/Admin Credentials (IMPORTANT)
ADMIN_EMAIL=admin@example.com
ADMIN_PASSWORD=Admin123!@#  # Max 72 characters

# Other settings...
LOG_LEVEL=INFO
ENVIRONMENT=production
```

## Need Help?

Agar `.env` file check karni hai, to:
1. File kholo
2. `ADMIN_EMAIL` aur `ADMIN_PASSWORD` search karo
3. Verify karo ke dono set hain aur password 72 chars se kam hai

