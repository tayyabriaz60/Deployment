# 🚀 Deployment Checklist

## Pre-Deployment

### 1. Environment Setup
- [ ] Copy `env.example` to `.env`
- [ ] Generate secure `SECRET_KEY`: `python -c "import secrets; print(secrets.token_urlsafe(64))"`
- [ ] Set `GOOGLE_API_KEY` from Google AI Studio
- [ ] Configure `DATABASE_URL` with production credentials
- [ ] Set strong `ADMIN_PASSWORD` (12+ chars, mixed case/numbers/symbols)
- [ ] Set `ENVIRONMENT=production` in `.env`

### 2. Database Setup
- [ ] Create PostgreSQL database
- [ ] Run migrations (if using Alembic) or let app create tables automatically
- [ ] Verify database connection

### 3. Security Configuration
- [ ] Update CORS origins in `app/main.py` (line 96) - restrict to your domain
- [ ] Update Socket.IO origins in `app/sockets/events.py` (line 18)
- [ ] Verify `.env` is in `.gitignore` and not committed
- [ ] Set up HTTPS/reverse proxy (nginx/traefik)

### 4. Dependencies
- [ ] Install Python 3.9+ 
- [ ] Create virtual environment: `python -m venv .venv`
- [ ] Activate venv: `.venv\Scripts\activate` (Windows) or `source .venv/bin/activate` (Linux)
- [ ] Install dependencies: `pip install -r requirements.txt`

## Deployment Steps

### 1. Production Server Setup
```bash
# Install system dependencies
sudo apt update
sudo apt install python3 python3-pip postgresql postgresql-contrib nginx

# Create project directory
sudo mkdir -p /var/www/medical-feedback
sudo chown $USER:$USER /var/www/medical-feedback
```

### 2. Application Deployment
```bash
# Clone/copy project files
cd /var/www/medical-feedback

# Setup virtual environment
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

# Configure environment
cp env.example .env
nano .env  # Edit with production values

# Create logs directory
mkdir -p logs
chmod 755 logs
```

### 3. Database Configuration
```bash
# Create database
sudo -u postgres psql
CREATE DATABASE feedback_db;
CREATE USER feedback_user WITH PASSWORD 'strong_password';
GRANT ALL PRIVILEGES ON DATABASE feedback_db TO feedback_user;
\q

# Update DATABASE_URL in .env
DATABASE_URL=postgresql+asyncpg://feedback_user:strong_password@localhost/feedback_db
```

### 4. Run Application
```bash
# Test run
uvicorn app.main:asgi_app --host 0.0.0.0 --port 8000

# Production with Gunicorn (recommended)
pip install gunicorn
gunicorn app.main:asgi_app -w 4 -k uvicorn.workers.UvicornWorker --bind 0.0.0.0:8000
```

### 5. Systemd Service (Linux)
Create `/etc/systemd/system/medical-feedback.service`:
```ini
[Unit]
Description=Medical Feedback Analysis Platform
After=network.target postgresql.service

[Service]
User=www-data
Group=www-data
WorkingDirectory=/var/www/medical-feedback
Environment="PATH=/var/www/medical-feedback/.venv/bin"
ExecStart=/var/www/medical-feedback/.venv/bin/gunicorn app.main:asgi_app -w 4 -k uvicorn.workers.UvicornWorker --bind 127.0.0.1:8000
Restart=always

[Install]
WantedBy=multi-user.target
```

Enable and start:
```bash
sudo systemctl daemon-reload
sudo systemctl enable medical-feedback
sudo systemctl start medical-feedback
sudo systemctl status medical-feedback
```

### 6. Nginx Configuration
Create `/etc/nginx/sites-available/medical-feedback`:
```nginx
server {
    listen 80;
    server_name your-domain.com;

    # Redirect HTTP to HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name your-domain.com;

    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /socket.io/ {
        proxy_pass http://127.0.0.1:8000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

Enable site:
```bash
sudo ln -s /etc/nginx/sites-available/medical-feedback /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

## Post-Deployment

### 1. Verification
- [ ] Test API endpoint: `https://your-domain.com/health`
- [ ] Test frontend: `https://your-domain.com/`
- [ ] Test Socket.IO connection
- [ ] Verify admin login works
- [ ] Test feedback submission
- [ ] Check logs: `tail -f logs/app.log`

### 2. Monitoring Setup
- [ ] Set up log rotation
- [ ] Configure monitoring (Prometheus/Grafana or similar)
- [ ] Set up alerts for errors
- [ ] Monitor database connection pool
- [ ] Track API response times

### 3. Backup Configuration
- [ ] Set up automated database backups
- [ ] Test backup restoration process
- [ ] Document backup schedule

### 4. Security Hardening
- [ ] Enable firewall (UFW/iptables)
- [ ] Restrict SSH access
- [ ] Set up fail2ban
- [ ] Regular security updates
- [ ] SSL certificate auto-renewal (Let's Encrypt)

## Maintenance

### Regular Tasks
- [ ] Monitor logs weekly
- [ ] Review error rates
- [ ] Check database performance
- [ ] Update dependencies monthly
- [ ] Review security patches
- [ ] Backup verification

### Updates
```bash
# Pull latest code
git pull origin main

# Activate venv
source .venv/bin/activate

# Update dependencies
pip install -r requirements.txt --upgrade

# Restart service
sudo systemctl restart medical-feedback

# Check status
sudo systemctl status medical-feedback
tail -f logs/app.log
```

## Troubleshooting

### Application won't start
- Check `.env` file exists and has all required variables
- Verify database connection
- Check logs: `tail -f logs/app.log`
- Verify SECRET_KEY is set and valid

### Database connection errors
- Verify PostgreSQL is running: `sudo systemctl status postgresql`
- Check DATABASE_URL in `.env`
- Verify database user permissions
- Check firewall rules

### Socket.IO not working
- Verify WebSocket proxy configuration in nginx
- Check CORS settings
- Verify Socket.IO origins in code

### High memory usage
- Reduce `DB_POOL_SIZE` if needed
- Monitor background tasks
- Check for memory leaks

## Support
For issues or questions, check:
- README.md for setup instructions
- CHANGELOG.md for recent changes
- Application logs in `logs/app.log`

