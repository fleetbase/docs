---
title: Deploy on the Cloud
sidebar_position: 2
slug: /deploying/cloud
---

# Deploy to Cloud

Deploy Fleetbase on any cloud provider using Docker containers. This guide provides step-by-step instructions for deploying Fleetbase on popular cloud platforms including AWS, Google Cloud Platform, Microsoft Azure, DigitalOcean, and others.

## Overview

Cloud deployment of Fleetbase involves setting up a containerized environment with the following components:
- **Application Server**: Main Fleetbase API and backend services
- **Database**: MySQL 8.0 for data persistence
- **Cache**: Redis for session storage and queue management
- **Web Server**: Nginx or similar for serving the frontend console
- **Real-time Service**: SocketCluster for live updates
- **Background Services**: Queue workers and scheduled tasks

## Prerequisites

Before starting the deployment, ensure you have:

### Cloud Infrastructure
- **Virtual Machine**: Minimum 2 vCPUs, 4GB RAM, 20GB storage
- **Operating System**: Ubuntu 20.04 LTS or later (recommended)
- **Network**: Public IP address and open ports (80, 443, 3306, 6379)
- **Domain Names**: Registered domains for API and console (optional but recommended)

### Required Software
- **Docker**: Version 20.10 or later
- **Docker Compose**: Version 2.0 or later
- **Git**: For cloning the repository
- **SSL Certificates**: For HTTPS (Let's Encrypt recommended)

### External Services (Optional)
- **Email Service**: AWS SES, SendGrid, Mailgun, or SMTP server
- **SMS Service**: Twilio account for SMS notifications
- **Maps Service**: Google Maps API key
- **Monitoring**: CloudWatch, Datadog, or similar

## Step 1: Provision Cloud Infrastructure

### AWS (Amazon Web Services)

1. **Launch EC2 Instance**:
   ```bash
   # Create EC2 instance with Ubuntu 20.04 LTS
   # Instance type: t3.medium or larger
   # Security group: Allow ports 22, 80, 443, 4200, 8000
   ```

2. **Configure Security Groups**:
   - SSH (22): Your IP address
   - HTTP (80): 0.0.0.0/0
   - HTTPS (443): 0.0.0.0/0
   - Custom (4200): 0.0.0.0/0 (Console)
   - Custom (8000): 0.0.0.0/0 (API)

3. **Attach Elastic IP** (recommended for production)

### Google Cloud Platform (GCP)

1. **Create Compute Engine Instance**:
   ```bash
   gcloud compute instances create fleetbase-server 
     --zone=us-central1-a 
     --machine-type=e2-medium 
     --image-family=ubuntu-2004-lts 
     --image-project=ubuntu-os-cloud 
     --boot-disk-size=20GB 
     --tags=fleetbase-server
   ```

2. **Configure Firewall Rules**:
   ```bash
   gcloud compute firewall-rules create fleetbase-http 
     --allow tcp:80,tcp:443,tcp:4200,tcp:8000 
     --target-tags fleetbase-server
   ```

### Microsoft Azure

1. **Create Virtual Machine**:
   ```bash
   az vm create 
     --resource-group myResourceGroup 
     --name fleetbase-vm 
     --image UbuntuLTS 
     --size Standard_B2s 
     --admin-username azureuser 
     --generate-ssh-keys
   ```

2. **Open Network Ports**:
   ```bash
   az vm open-port --port 80,443,4200,8000 --resource-group myResourceGroup --name fleetbase-vm
   ```

### DigitalOcean

1. **Create Droplet**:
   - Choose Ubuntu 20.04 LTS
   - Select $20/month plan (2 vCPUs, 4GB RAM)
   - Add SSH key
   - Enable monitoring

2. **Configure Firewall**:
   - Create firewall rules for ports 22, 80, 443, 4200, 8000

## Step 2: Server Setup and Dependencies

### Connect to Your Server

```bash
# Replace with your server's IP address
ssh root@YOUR_SERVER_IP
```

### Update System Packages

```bash
# Update package list and upgrade system
sudo apt update && sudo apt upgrade -y

# Install essential packages
sudo apt install -y curl wget git unzip software-properties-common apt-transport-https ca-certificates gnupg lsb-release
```

### Install Docker

```bash
# Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Add Docker repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io

# Add current user to docker group
sudo usermod -aG docker $USER

# Start and enable Docker
sudo systemctl start docker
sudo systemctl enable docker
```

### Install Docker Compose

```bash
# Download Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# Make executable
sudo chmod +x /usr/local/bin/docker-compose

# Verify installation
docker-compose --version
```

### Configure Firewall (UFW)

```bash
# Enable UFW
sudo ufw enable

# Allow SSH
sudo ufw allow ssh

# Allow HTTP and HTTPS
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Allow Fleetbase ports
sudo ufw allow 4200/tcp  # Console
sudo ufw allow 8000/tcp  # API

# Check status
sudo ufw status
```

## Step 3: Deploy Fleetbase

### Clone Repository

```bash
# Clone Fleetbase repository
git clone https://github.com/fleetbase/fleetbase.git
cd fleetbase
```

### Configure Environment Variables

Create production environment configuration:

```bash
# Create docker-compose override file
cat > docker-compose.override.yml << 'EOF'
version: '3.8'
services:
  application:
    environment:
      - APP_ENV=production
      - APP_URL=https://api.yourdomain.com
      - CONSOLE_HOST=https://console.yourdomain.com
      - DATABASE_URL=mysql://fleetbase:secure_password@database:3306/fleetbase
      - REDIS_URL=redis://cache:6379
      - MAIL_MAILER=smtp
      - MAIL_HOST=smtp.yourmailprovider.com
      - MAIL_PORT=587
      - MAIL_USERNAME=your_email@domain.com
      - MAIL_PASSWORD=your_email_password
      - MAIL_FROM_NAME=Fleetbase
      - MAIL_FROM_ADDRESS=noreply@yourdomain.com
      - GOOGLE_MAPS_API_KEY=your_google_maps_api_key
      - TWILIO_SID=your_twilio_sid
      - TWILIO_TOKEN=your_twilio_token
      - TWILIO_FROM=+1234567890
      - OSRM_HOST=https://router.project-osrm.org
    volumes:
      - ./api/.env:/fleetbase/api/.env
  
  console:
    environment:
      - API_HOST=https://api.yourdomain.com
      - SOCKETCLUSTER_HOST=https://api.yourdomain.com
      - SOCKETCLUSTER_PORT=8000
      - IS_SECURE=true
    volumes:
      - ./console/fleetbase.config.json:/usr/share/nginx/html/fleetbase.config.json

  database:
    environment:
      - MYSQL_ROOT_PASSWORD=secure_root_password
      - MYSQL_DATABASE=fleetbase
      - MYSQL_USER=fleetbase
      - MYSQL_PASSWORD=secure_password
    volumes:
      - fleetbase_db_data:/var/lib/mysql

  cache:
    volumes:
      - fleetbase_redis_data:/data

volumes:
  fleetbase_db_data:
  fleetbase_redis_data:
EOF
```

### Create API Environment File

```bash
# Create API environment file
mkdir -p api
cat > api/.env << 'EOF'
APP_NAME=Fleetbase
APP_ENV=production
APP_KEY=base64:GENERATE_THIS_KEY
APP_DEBUG=false
APP_URL=https://api.yourdomain.com

LOG_CHANNEL=stdout
LOG_LEVEL=info

DATABASE_URL=mysql://fleetbase:secure_password@database:3306/fleetbase

CACHE_DRIVER=redis
CACHE_URL=redis://cache:6379
SESSION_DRIVER=redis
SESSION_DOMAIN=.yourdomain.com

QUEUE_CONNECTION=redis
REDIS_URL=redis://cache:6379

BROADCAST_DRIVER=socketcluster
SOCKETCLUSTER_HOST=api.yourdomain.com
SOCKETCLUSTER_PORT=8000
SOCKETCLUSTER_SECURE=true

MAIL_MAILER=smtp
MAIL_HOST=smtp.yourmailprovider.com
MAIL_PORT=587
MAIL_USERNAME=your_email@domain.com
MAIL_PASSWORD=your_email_password
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=noreply@yourdomain.com
MAIL_FROM_NAME=Fleetbase

CONSOLE_HOST=https://console.yourdomain.com

GOOGLE_MAPS_API_KEY=your_google_maps_api_key
GOOGLE_MAPS_LOCALE=us

TWILIO_SID=your_twilio_sid
TWILIO_TOKEN=your_twilio_token
TWILIO_FROM=+1234567890

IPINFO_API_KEY=your_ipinfo_api_key

OSRM_HOST=https://router.project-osrm.org

REGISTRY_HOST=https://registry.fleetbase.io
REGISTRY_PREINSTALLED_EXTENSIONS=true
EOF
```

### Create Console Configuration

```bash
# Create console configuration
mkdir -p console
cat > console/fleetbase.config.json << 'EOF'
{
  "API_HOST": "https://api.yourdomain.com",
  "SOCKETCLUSTER_HOST": "https://api.yourdomain.com",
  "SOCKETCLUSTER_PORT": 8000,
  "SOCKETCLUSTER_PATH": "/socket/",
  "IS_SECURE": true,
  "APP_NAME": "Fleetbase"
}
EOF
```

### Generate Application Key

```bash
# Generate application key
docker run --rm fleetbase/fleetbase:latest php artisan key:generate --show

# Copy the generated key and update it in api/.env file
# Replace APP_KEY=base64:GENERATE_THIS_KEY with the generated key
```

## Step 4: SSL Certificate Setup

### Option 1: Let's Encrypt (Recommended)

```bash
# Install Certbot
sudo apt install -y certbot

# Generate certificates for your domains
sudo certbot certonly --standalone -d api.yourdomain.com
sudo certbot certonly --standalone -d console.yourdomain.com

# Certificates will be stored in /etc/letsencrypt/live/
```

### Option 2: Custom SSL Certificates

If you have custom SSL certificates, place them in:
- `/etc/ssl/certs/api.yourdomain.com.crt`
- `/etc/ssl/private/api.yourdomain.com.key`
- `/etc/ssl/certs/console.yourdomain.com.crt`
- `/etc/ssl/private/console.yourdomain.com.key`

## Step 5: Configure Reverse Proxy

Create Nginx configuration for SSL termination and reverse proxy:

```bash
# Install Nginx
sudo apt install -y nginx

# Create API configuration
sudo cat > /etc/nginx/sites-available/fleetbase-api << 'EOF'
server {
    listen 80;
    server_name api.yourdomain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name api.yourdomain.com;

    ssl_certificate /etc/letsencrypt/live/api.yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/api.yourdomain.com/privkey.pem;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512;
    ssl_prefer_server_ciphers off;

    location / {
        proxy_pass http://localhost:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /socket/ {
        proxy_pass http://localhost:8000/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
EOF

# Create Console configuration
sudo cat > /etc/nginx/sites-available/fleetbase-console << 'EOF'
server {
    listen 80;
    server_name console.yourdomain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name console.yourdomain.com;

    ssl_certificate /etc/letsencrypt/live/console.yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/console.yourdomain.com/privkey.pem;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512;
    ssl_prefer_server_ciphers off;

    location / {
        proxy_pass http://localhost:4200;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
EOF

# Enable sites
sudo ln -s /etc/nginx/sites-available/fleetbase-api /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/fleetbase-console /etc/nginx/sites-enabled/

# Remove default site
sudo rm /etc/nginx/sites-enabled/default

# Test configuration
sudo nginx -t

# Restart Nginx
sudo systemctl restart nginx
sudo systemctl enable nginx
```

## Step 6: Deploy Services

### Build and Start Services

```bash
# Build and start all services
docker-compose up --build -d

# Check service status
docker-compose ps

# View logs
docker-compose logs -f
```

### Initialize Database

```bash
# Access application container
docker-compose exec application bash

# Run deployment script
sh deploy.sh

# Exit container
exit
```

## Step 7: Configure DNS

Update your DNS records to point to your server:

```
A Record: api.yourdomain.com → YOUR_SERVER_IP
A Record: console.yourdomain.com → YOUR_SERVER_IP
```

## Step 8: Verify Deployment

### Check Services

```bash
# Check all containers are running
docker-compose ps

# Check logs for any errors
docker-compose logs

# Test database connection
docker-compose exec database mysql -u fleetbase -p fleetbase

# Test Redis connection
docker-compose exec cache redis-cli ping
```

### Access Applications

1. **API**: https://api.yourdomain.com
2. **Console**: https://console.yourdomain.com

### Test Functionality

- Create admin account
- Test login/logout
- Verify real-time updates
- Check email notifications
- Test API endpoints

## Step 9: Production Optimizations

### Performance Tuning

```bash
# Optimize MySQL configuration
sudo cat >> /etc/mysql/mysql.conf.d/fleetbase.cnf << 'EOF'
[mysqld]
innodb_buffer_pool_size = 1G
innodb_log_file_size = 256M
innodb_flush_log_at_trx_commit = 2
query_cache_type = 1
query_cache_size = 256M
max_connections = 200
EOF

# Restart MySQL
sudo systemctl restart mysql
```

### Security Hardening

```bash
# Update system packages regularly
sudo apt update && sudo apt upgrade -y

# Configure automatic security updates
sudo apt install -y unattended-upgrades
sudo dpkg-reconfigure -plow unattended-upgrades

# Secure MySQL installation
sudo mysql_secure_installation

# Configure fail2ban
sudo apt install -y fail2ban
sudo systemctl enable fail2ban
```

### Monitoring Setup

```bash
# Install monitoring tools
sudo apt install -y htop iotop nethogs

# Set up log rotation
sudo cat > /etc/logrotate.d/fleetbase << 'EOF'
/var/log/fleetbase/*.log {
    daily
    missingok
    rotate 30
    compress
    delaycompress
    notifempty
    create 0644 www-data www-data
}
EOF
```

## Step 10: Backup and Maintenance

### Database Backup

```bash
# Create backup script
cat > /home/ubuntu/backup-fleetbase.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="/home/ubuntu/backups"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p $BACKUP_DIR

# Backup database
docker-compose exec -T database mysqldump -u fleetbase -psecure_password fleetbase > $BACKUP_DIR/fleetbase_$DATE.sql

# Backup Redis data
docker-compose exec -T cache redis-cli BGSAVE
docker cp fleetbase_cache_1:/data/dump.rdb $BACKUP_DIR/redis_$DATE.rdb

# Compress backups
tar -czf $BACKUP_DIR/fleetbase_backup_$DATE.tar.gz $BACKUP_DIR/fleetbase_$DATE.sql $BACKUP_DIR/redis_$DATE.rdb

# Remove old backups (keep 30 days)
find $BACKUP_DIR -name "fleetbase_backup_*.tar.gz" -mtime +30 -delete

echo "Backup completed: fleetbase_backup_$DATE.tar.gz"
EOF

chmod +x /home/ubuntu/backup-fleetbase.sh

# Schedule daily backups
(crontab -l 2>/dev/null; echo "0 2 * * * /home/ubuntu/backup-fleetbase.sh") | crontab -
```

### Update Procedure

```bash
# Create update script
cat > /home/ubuntu/update-fleetbase.sh << 'EOF'
#!/bin/bash
cd /home/ubuntu/fleetbase

# Backup before update
/home/ubuntu/backup-fleetbase.sh

# Pull latest changes
git pull origin main

# Rebuild and restart services
docker-compose down
docker-compose up --build -d

# Run any pending migrations
docker-compose exec application php artisan migrate --force

echo "Fleetbase updated successfully"
EOF

chmod +x /home/ubuntu/update-fleetbase.sh
```

## Troubleshooting

### Common Issues

1. **Services not starting**:
   ```bash
   # Check logs
   docker-compose logs
   
   # Restart services
   docker-compose restart
   ```

2. **Database connection errors**:
   ```bash
   # Check database status
   docker-compose exec database mysql -u root -p
   
   # Reset database password
   docker-compose exec database mysql -u root -p -e "ALTER USER 'fleetbase'@'%' IDENTIFIED BY 'new_password';"
   ```

3. **SSL certificate issues**:
   ```bash
   # Renew Let's Encrypt certificates
   sudo certbot renew
   
   # Restart Nginx
   sudo systemctl restart nginx
   ```

4. **Performance issues**:
   ```bash
   # Monitor resource usage
   htop
   docker stats
   
   # Check disk space
   df -h
   ```

### Log Locations

- **Application logs**: `docker-compose logs application`
- **Database logs**: `docker-compose logs database`
- **Nginx logs**: `/var/log/nginx/`
- **System logs**: `/var/log/syslog`

## Conclusion

You now have a fully functional Fleetbase deployment running on your cloud infrastructure. The setup includes:

- ✅ **Secure HTTPS** with SSL certificates
- ✅ **Production-ready** database and cache
- ✅ **Real-time functionality** with SocketCluster
- ✅ **Automated backups** and maintenance
- ✅ **Monitoring and logging** setup
- ✅ **Security hardening** measures

For additional support and advanced configurations, visit the [Fleetbase documentation](https://docs.fleetbase.io) or join our [Discord community](https://discord.gg/V7RVWRQ2Wm).

### Next Steps

1. **Configure Extensions**: Install additional Fleetbase extensions from the registry
2. **Set up Monitoring**: Implement comprehensive monitoring with tools like Prometheus/Grafana
3. **Scale Infrastructure**: Consider load balancing and database clustering for high-traffic deployments
4. **Backup Strategy**: Implement off-site backups to cloud storage
5. **CI/CD Pipeline**: Set up automated deployment pipelines for updates

