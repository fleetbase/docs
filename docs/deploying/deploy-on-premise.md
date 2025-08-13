---
title: Deploy on Premise
sidebar_position: 2
slug: /deploying/on-premise
---

# Deploy on Premise

Deploy Fleetbase on your own infrastructure with complete control over your data and environment. This guide covers both Docker-based and native installations for on-premise deployments.

## Overview

On-premise deployment gives you full control over your Fleetbase installation, ensuring data sovereignty, compliance with internal policies, and customization capabilities. This guide covers:

- **Docker Deployment**: Containerized setup for easy management
- **Native Installation**: Direct installation on the host system
- **Security Configuration**: Enterprise-grade security measures
- **High Availability**: Multi-server setup for production environments
- **Backup and Recovery**: Comprehensive data protection strategies

## Prerequisites

### Hardware Requirements

#### Minimum Requirements (Development/Testing)
- **CPU**: 2 cores (2.4 GHz)
- **RAM**: 4GB
- **Storage**: 50GB SSD
- **Network**: 100 Mbps

#### Recommended Requirements (Production)
- **CPU**: 4+ cores (2.8 GHz)
- **RAM**: 8GB+ (16GB for high traffic)
- **Storage**: 200GB+ SSD with RAID 1
- **Network**: 1 Gbps with redundancy

#### High Availability Setup
- **Load Balancer**: 2 servers (2 cores, 4GB RAM each)
- **Application Servers**: 2+ servers (4 cores, 8GB RAM each)
- **Database Servers**: 2 servers (4 cores, 16GB RAM each)
- **Shared Storage**: NFS or SAN for file uploads

### Software Requirements

- **Operating System**: Ubuntu 20.04 LTS, CentOS 8, or RHEL 8
- **Docker**: Version 20.10+ (for Docker deployment)
- **Docker Compose**: Version 2.0+
- **Git**: For source code management
- **SSL Certificates**: For HTTPS encryption

### Network Requirements

- **Firewall Configuration**: Controlled access to required ports
- **DNS Resolution**: Internal DNS or hosts file configuration
- **Internet Access**: For initial setup and updates (can be restricted post-deployment)

## ⭐️ Docker Deployment

### Step 1: System Preparation

#### Update System

```bash
# Ubuntu/Debian
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl wget git unzip software-properties-common

# CentOS/RHEL
sudo yum update -y
sudo yum install -y curl wget git unzip
```

#### Install Docker

```bash
# Ubuntu/Debian
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER

# CentOS/RHEL
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install -y docker-ce docker-ce-cli containerd.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
```

#### Install Docker Compose

```bash
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Verify installation
docker-compose --version
```

### Step 2: Firewall Configuration

```bash
# Ubuntu (UFW)
sudo ufw enable
sudo ufw allow ssh
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 4200/tcp  # Console
sudo ufw allow 8000/tcp  # API

# CentOS/RHEL (firewalld)
sudo systemctl start firewalld
sudo systemctl enable firewalld
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --permanent --add-port=4200/tcp
sudo firewall-cmd --permanent --add-port=8000/tcp
sudo firewall-cmd --reload
```

### Step 3: Clone and Configure Fleetbase

```bash
# Clone repository
git clone https://github.com/fleetbase/fleetbase.git
cd fleetbase

# Create production environment directory
mkdir -p /opt/fleetbase
sudo cp -r * /opt/fleetbase/
sudo chown -R $USER:$USER /opt/fleetbase
cd /opt/fleetbase
```

### Step 4: Production Configuration

#### Create Docker Compose Override

```bash
cat > docker-compose.override.yml << 'EOF'
version: '3.8'

services:
  application:
    restart: unless-stopped
    environment:
      - APP_ENV=production
      - APP_DEBUG=false
      - APP_URL=https://fleetbase.company.local
      - CONSOLE_HOST=https://console.company.local
      - DATABASE_URL=mysql://fleetbase:CHANGE_THIS_PASSWORD@database:3306/fleetbase
      - REDIS_URL=redis://cache:6379
      - LOG_CHANNEL=daily
      - LOG_LEVEL=info
      - MAIL_MAILER=smtp
      - MAIL_HOST=mail.company.local
      - MAIL_PORT=587
      - MAIL_USERNAME=fleetbase@company.local
      - MAIL_PASSWORD=CHANGE_THIS_PASSWORD
      - MAIL_FROM_NAME=Fleetbase
      - MAIL_FROM_ADDRESS=noreply@company.local
      - GOOGLE_MAPS_API_KEY=YOUR_GOOGLE_MAPS_API_KEY
      - OSRM_HOST=https://router.project-osrm.org
    volumes:
      - /opt/fleetbase/storage/app:/fleetbase/api/storage/app
      - /opt/fleetbase/storage/logs:/fleetbase/api/storage/logs
    networks:
      - fleetbase-network

  console:
    restart: unless-stopped
    environment:
      - API_HOST=https://fleetbase.company.local
      - SOCKETCLUSTER_HOST=https://fleetbase.company.local
      - SOCKETCLUSTER_PORT=8000
      - IS_SECURE=true
    networks:
      - fleetbase-network

  database:
    restart: unless-stopped
    environment:
      - MYSQL_ROOT_PASSWORD=CHANGE_THIS_ROOT_PASSWORD
      - MYSQL_DATABASE=fleetbase
      - MYSQL_USER=fleetbase
      - MYSQL_PASSWORD=CHANGE_THIS_PASSWORD
    volumes:
      - /opt/fleetbase/data/mysql:/var/lib/mysql
      - /opt/fleetbase/backups/mysql:/backups
    command: [
      'mysqld',
      '--character-set-server=utf8mb4',
      '--collation-server=utf8mb4_unicode_ci',
      '--innodb-buffer-pool-size=1G',
      '--innodb-log-file-size=256M',
      '--max-connections=200'
    ]
    networks:
      - fleetbase-network

  cache:
    restart: unless-stopped
    volumes:
      - /opt/fleetbase/data/redis:/data
    command: redis-server --appendonly yes --maxmemory 512mb --maxmemory-policy allkeys-lru
    networks:
      - fleetbase-network

  socket:
    restart: unless-stopped
    networks:
      - fleetbase-network

  scheduler:
    restart: unless-stopped
    volumes:
      - /opt/fleetbase/storage/logs:/fleetbase/api/storage/logs
    networks:
      - fleetbase-network

  queue:
    restart: unless-stopped
    volumes:
      - /opt/fleetbase/storage/logs:/fleetbase/api/storage/logs
    networks:
      - fleetbase-network

networks:
  fleetbase-network:
    driver: bridge

volumes:
  mysql-data:
    driver: local
  redis-data:
    driver: local
EOF
```

#### Create Directory Structure

```bash
# Create required directories
sudo mkdir -p /opt/fleetbase/{data/{mysql,redis},storage/{app,logs},backups/{mysql,files},ssl}

# Set proper permissions
sudo chown -R 999:999 /opt/fleetbase/data/mysql
sudo chown -R 999:999 /opt/fleetbase/data/redis
sudo chown -R www-data:www-data /opt/fleetbase/storage
sudo chmod -R 755 /opt/fleetbase/storage
```

#### Configure Environment Variables

```bash
# Create API environment file
mkdir -p api
cat > api/.env << 'EOF'
APP_NAME="Fleetbase Enterprise"
APP_ENV=production
APP_KEY=base64:GENERATE_32_CHARACTER_KEY_HERE
APP_DEBUG=false
APP_URL=https://fleetbase.company.local

LOG_CHANNEL=daily
LOG_LEVEL=info

DATABASE_URL=mysql://fleetbase:CHANGE_THIS_PASSWORD@database:3306/fleetbase

CACHE_DRIVER=redis
CACHE_URL=redis://cache:6379
SESSION_DRIVER=redis
SESSION_DOMAIN=.company.local

QUEUE_CONNECTION=redis
REDIS_URL=redis://cache:6379

BROADCAST_DRIVER=socketcluster
SOCKETCLUSTER_HOST=fleetbase.company.local
SOCKETCLUSTER_PORT=8000
SOCKETCLUSTER_SECURE=true

FILESYSTEM_DISK=local
FILESYSTEM_CLOUD=local

MAIL_MAILER=smtp
MAIL_HOST=mail.company.local
MAIL_PORT=587
MAIL_USERNAME=fleetbase@company.local
MAIL_PASSWORD=CHANGE_THIS_PASSWORD
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=noreply@company.local
MAIL_FROM_NAME="Fleetbase Enterprise"

CONSOLE_HOST=https://console.company.local

GOOGLE_MAPS_API_KEY=YOUR_GOOGLE_MAPS_API_KEY
GOOGLE_MAPS_LOCALE=us

OSRM_HOST=https://router.project-osrm.org

REGISTRY_HOST=https://registry.fleetbase.io
REGISTRY_PREINSTALLED_EXTENSIONS=true

# Security settings
SESSION_LIFETIME=120
SESSION_ENCRYPT=true
BCRYPT_ROUNDS=12

# Rate limiting
THROTTLE_REQUESTS=60
THROTTLE_DECAY_MINUTES=1
EOF
```

### Step 5: SSL Certificate Setup

#### Option 1: Self-Signed Certificates (Internal Use)

```bash
# Create SSL directory
sudo mkdir -p /opt/fleetbase/ssl

# Generate private key
sudo openssl genrsa -out /opt/fleetbase/ssl/fleetbase.key 2048

# Create certificate signing request
sudo openssl req -new -key /opt/fleetbase/ssl/fleetbase.key -out /opt/fleetbase/ssl/fleetbase.csr -subj "/C=US/ST=State/L=City/O=Company/CN=fleetbase.company.local"

# Generate self-signed certificate
sudo openssl x509 -req -days 365 -in /opt/fleetbase/ssl/fleetbase.csr -signkey /opt/fleetbase/ssl/fleetbase.key -out /opt/fleetbase/ssl/fleetbase.crt

# Set permissions
sudo chmod 600 /opt/fleetbase/ssl/fleetbase.key
sudo chmod 644 /opt/fleetbase/ssl/fleetbase.crt
```

#### Option 2: Corporate CA Certificates

```bash
# Copy your corporate certificates
sudo cp /path/to/your/certificate.crt /opt/fleetbase/ssl/fleetbase.crt
sudo cp /path/to/your/private.key /opt/fleetbase/ssl/fleetbase.key
sudo cp /path/to/your/ca-bundle.crt /opt/fleetbase/ssl/ca-bundle.crt

# Set proper permissions
sudo chmod 600 /opt/fleetbase/ssl/fleetbase.key
sudo chmod 644 /opt/fleetbase/ssl/fleetbase.crt
sudo chmod 644 /opt/fleetbase/ssl/ca-bundle.crt
```

### Step 6: Reverse Proxy Configuration

#### Install and Configure Nginx

```bash
# Install Nginx
# Ubuntu/Debian
sudo apt install -y nginx

# CentOS/RHEL
sudo yum install -y nginx

# Create Fleetbase configuration
sudo cat > /etc/nginx/sites-available/fleetbase << 'EOF'
# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name fleetbase.company.local console.company.local;
    return 301 https://$server_name$request_uri;
}

# API Server
server {
    listen 443 ssl http2;
    server_name fleetbase.company.local;

    ssl_certificate /opt/fleetbase/ssl/fleetbase.crt;
    ssl_certificate_key /opt/fleetbase/ssl/fleetbase.key;
    
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512:ECDHE-RSA-AES256-GCM-SHA384;
    ssl_prefer_server_ciphers off;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;

    # Security headers
    add_header X-Frame-Options DENY;
    add_header X-Content-Type-Options nosniff;
    add_header X-XSS-Protection "1; mode=block";
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    # API endpoints
    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Port $server_port;
        
        # Timeouts
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
        
        # Buffer settings
        proxy_buffering on;
        proxy_buffer_size 128k;
        proxy_buffers 4 256k;
        proxy_busy_buffers_size 256k;
    }

    # WebSocket support for real-time features
    location /socket/ {
        proxy_pass http://127.0.0.1:8000/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # WebSocket timeouts
        proxy_read_timeout 86400;
    }

    # File upload size
    client_max_body_size 100M;

    # Logging
    access_log /var/log/nginx/fleetbase-api-access.log;
    error_log /var/log/nginx/fleetbase-api-error.log;
}

# Console Server
server {
    listen 443 ssl http2;
    server_name console.company.local;

    ssl_certificate /opt/fleetbase/ssl/fleetbase.crt;
    ssl_certificate_key /opt/fleetbase/ssl/fleetbase.key;
    
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512:ECDHE-RSA-AES256-GCM-SHA384;
    ssl_prefer_server_ciphers off;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;

    # Security headers
    add_header X-Frame-Options DENY;
    add_header X-Content-Type-Options nosniff;
    add_header X-XSS-Protection "1; mode=block";
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    location / {
        proxy_pass http://127.0.0.1:4200;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Port $server_port;
    }

    # Logging
    access_log /var/log/nginx/fleetbase-console-access.log;
    error_log /var/log/nginx/fleetbase-console-error.log;
}
EOF

# Enable site (Ubuntu/Debian)
sudo ln -s /etc/nginx/sites-available/fleetbase /etc/nginx/sites-enabled/
sudo rm -f /etc/nginx/sites-enabled/default

# For CentOS/RHEL, copy to conf.d
sudo cp /etc/nginx/sites-available/fleetbase /etc/nginx/conf.d/fleetbase.conf

# Test configuration
sudo nginx -t

# Start and enable Nginx
sudo systemctl start nginx
sudo systemctl enable nginx
```

### Step 7: Deploy Services

```bash
# Generate application key
APP_KEY=$(docker run --rm fleetbase/fleetbase:latest php artisan key:generate --show)
echo "Generated APP_KEY: $APP_KEY"

# Update the APP_KEY in api/.env file
sed -i "s/APP_KEY=base64:GENERATE_32_CHARACTER_KEY_HERE/APP_KEY=$APP_KEY/" api/.env

# Start services
docker-compose up --build -d

# Wait for services to start
sleep 30

# Check service status
docker-compose ps

# Initialize database
docker-compose exec application bash -c "cd /fleetbase/api && php artisan migrate --force"
docker-compose exec application bash -c "cd /fleetbase/api && php artisan db:seed --force"

# Create admin user
docker-compose exec application bash -c "cd /fleetbase/api && php artisan fleetbase:create-user"
```

### Step 8: DNS Configuration

Add entries to your internal DNS server or hosts file:

```bash
# Add to /etc/hosts on client machines
echo "YOUR_SERVER_IP fleetbase.company.local" | sudo tee -a /etc/hosts
echo "YOUR_SERVER_IP console.company.local" | sudo tee -a /etc/hosts
```

## Native Installation

### Step 1: Install Dependencies

#### Install PHP and Extensions

```bash
# Ubuntu/Debian
sudo apt install -y software-properties-common
sudo add-apt-repository ppa:ondrej/php
sudo apt update
sudo apt install -y php8.1 php8.1-fpm php8.1-mysql php8.1-redis php8.1-gd php8.1-xml php8.1-mbstring php8.1-curl php8.1-zip php8.1-bcmath php8.1-intl

# CentOS/RHEL
sudo yum install -y epel-release
sudo yum install -y https://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo yum module enable php:remi-8.1
sudo yum install -y php php-fpm php-mysql php-redis php-gd php-xml php-mbstring php-curl php-zip php-bcmath php-intl
```

#### Install MySQL

```bash
# Ubuntu/Debian
sudo apt install -y mysql-server-8.0

# CentOS/RHEL
sudo yum install -y mysql-server
sudo systemctl start mysqld
sudo systemctl enable mysqld

# Secure MySQL installation
sudo mysql_secure_installation
```

#### Install Redis

```bash
# Ubuntu/Debian
sudo apt install -y redis-server

# CentOS/RHEL
sudo yum install -y redis
sudo systemctl start redis
sudo systemctl enable redis
```

#### Install Node.js (for console)

```bash
# Install Node.js 18.x
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs

# Install pnpm
sudo npm install -g pnpm
```

### Step 2: Database Setup

```bash
# Create database and user
sudo mysql -u root -p << 'EOF'
CREATE DATABASE fleetbase CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'fleetbase'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON fleetbase.* TO 'fleetbase'@'localhost';
FLUSH PRIVILEGES;
EXIT;
EOF
```

### Step 3: Install Fleetbase

```bash
# Create application directory
sudo mkdir -p /var/www/fleetbase
sudo chown -R www-data:www-data /var/www/fleetbase

# Clone repository
sudo -u www-data git clone https://github.com/fleetbase/fleetbase.git /var/www/fleetbase
cd /var/www/fleetbase

# Install Composer
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer

# Install API dependencies
cd /var/www/fleetbase/api
sudo -u www-data composer install --no-dev --optimize-autoloader

# Install Console dependencies
cd /var/www/fleetbase/console
sudo -u www-data pnpm install

# Build console for production
sudo -u www-data pnpm build --environment=production
```

### Step 4: Configure Services

#### Configure PHP-FPM

```bash
# Edit PHP-FPM pool configuration
sudo cat > /etc/php/8.1/fpm/pool.d/fleetbase.conf << 'EOF'
[fleetbase]
user = www-data
group = www-data
listen = /run/php/php8.1-fpm-fleetbase.sock
listen.owner = www-data
listen.group = www-data
pm = dynamic
pm.max_children = 50
pm.start_servers = 5
pm.min_spare_servers = 5
pm.max_spare_servers = 35
pm.process_idle_timeout = 10s
pm.max_requests = 500
chdir = /var/www/fleetbase/api
EOF

# Restart PHP-FPM
sudo systemctl restart php8.1-fpm
sudo systemctl enable php8.1-fpm
```

#### Configure Nginx for Native Installation

```bash
sudo cat > /etc/nginx/sites-available/fleetbase-native << 'EOF'
server {
    listen 80;
    server_name fleetbase.company.local console.company.local;
    return 301 https://$server_name$request_uri;
}

# API Server
server {
    listen 443 ssl http2;
    server_name fleetbase.company.local;
    root /var/www/fleetbase/api/public;
    index index.php;

    ssl_certificate /opt/fleetbase/ssl/fleetbase.crt;
    ssl_certificate_key /opt/fleetbase/ssl/fleetbase.key;
    
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512;
    ssl_prefer_server_ciphers off;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ .php$ {
        fastcgi_pass unix:/run/php/php8.1-fpm-fleetbase.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /.ht {
        deny all;
    }

    client_max_body_size 100M;
}

# Console Server
server {
    listen 443 ssl http2;
    server_name console.company.local;
    root /var/www/fleetbase/console/dist;
    index index.html;

    ssl_certificate /opt/fleetbase/ssl/fleetbase.crt;
    ssl_certificate_key /opt/fleetbase/ssl/fleetbase.key;
    
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512;
    ssl_prefer_server_ciphers off;

    location / {
        try_files $uri $uri/ /index.html;
    }

    location ~* .(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
EOF

sudo ln -s /etc/nginx/sites-available/fleetbase-native /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

## High Availability Setup

### Load Balancer Configuration

```bash
# Install HAProxy
sudo apt install -y haproxy

# Configure HAProxy
sudo cat > /etc/haproxy/haproxy.cfg << 'EOF'
global
    daemon
    chroot /var/lib/haproxy
    stats socket /run/haproxy/admin.sock mode 660 level admin
    stats timeout 30s
    user haproxy
    group haproxy

defaults
    mode http
    timeout connect 5000ms
    timeout client 50000ms
    timeout server 50000ms
    option httplog

frontend fleetbase-frontend
    bind *:80
    bind *:443 ssl crt /opt/fleetbase/ssl/fleetbase.pem
    redirect scheme https if !{ ssl_fc }
    
    acl is_api hdr(host) -i fleetbase.company.local
    acl is_console hdr(host) -i console.company.local
    
    use_backend fleetbase-api if is_api
    use_backend fleetbase-console if is_console

backend fleetbase-api
    balance roundrobin
    option httpchk GET /health
    server api1 10.0.1.10:8000 check
    server api2 10.0.1.11:8000 check

backend fleetbase-console
    balance roundrobin
    option httpchk GET /
    server console1 10.0.1.10:4200 check
    server console2 10.0.1.11:4200 check

listen stats
    bind *:8404
    stats enable
    stats uri /stats
    stats refresh 30s
EOF

sudo systemctl restart haproxy
sudo systemctl enable haproxy
```

### Database Clustering (MySQL Master-Slave)

```bash
# Master server configuration
sudo cat >> /etc/mysql/mysql.conf.d/master.cnf << 'EOF'
[mysqld]
server-id = 1
log-bin = mysql-bin
binlog-do-db = fleetbase
EOF

# Slave server configuration
sudo cat >> /etc/mysql/mysql.conf.d/slave.cnf << 'EOF'
[mysqld]
server-id = 2
relay-log = mysql-relay-bin
log-bin = mysql-bin
binlog-do-db = fleetbase
read-only = 1
EOF
```

## Security Hardening

### System Security

```bash
# Install security tools
sudo apt install -y fail2ban ufw aide

# Configure fail2ban
sudo cat > /etc/fail2ban/jail.local << 'EOF'
[DEFAULT]
bantime = 3600
findtime = 600
maxretry = 3

[sshd]
enabled = true

[nginx-http-auth]
enabled = true

[nginx-limit-req]
enabled = true
EOF

sudo systemctl restart fail2ban
sudo systemctl enable fail2ban

# Configure file integrity monitoring
sudo aideinit
sudo mv /var/lib/aide/aide.db.new /var/lib/aide/aide.db
```

### Application Security

```bash
# Set proper file permissions
sudo chown -R www-data:www-data /var/www/fleetbase
sudo find /var/www/fleetbase -type f -exec chmod 644 {} ;
sudo find /var/www/fleetbase -type d -exec chmod 755 {} ;
sudo chmod -R 775 /var/www/fleetbase/api/storage
sudo chmod -R 775 /var/www/fleetbase/api/bootstrap/cache

# Secure sensitive files
sudo chmod 600 /var/www/fleetbase/api/.env
sudo chown root:root /var/www/fleetbase/api/.env
```

## Backup and Recovery

### Automated Backup Script

```bash
# Create backup script
sudo cat > /opt/fleetbase/backup.sh << 'EOF'
#!/bin/bash

BACKUP_DIR="/opt/fleetbase/backups"
DATE=$(date +%Y%m%d_%H%M%S)
RETENTION_DAYS=30

# Create backup directory
mkdir -p $BACKUP_DIR/{mysql,files,config}

# Database backup
mysqldump -u fleetbase -psecure_password fleetbase > $BACKUP_DIR/mysql/fleetbase_$DATE.sql

# File backup
tar -czf $BACKUP_DIR/files/storage_$DATE.tar.gz /var/www/fleetbase/api/storage

# Configuration backup
cp /var/www/fleetbase/api/.env $BACKUP_DIR/config/env_$DATE
cp /etc/nginx/sites-available/fleetbase $BACKUP_DIR/config/nginx_$DATE

# Compress all backups
tar -czf $BACKUP_DIR/fleetbase_full_backup_$DATE.tar.gz -C $BACKUP_DIR mysql files config

# Clean old backups
find $BACKUP_DIR -name "fleetbase_full_backup_*.tar.gz" -mtime +$RETENTION_DAYS -delete

# Log backup completion
echo "$(date): Backup completed - fleetbase_full_backup_$DATE.tar.gz" >> /var/log/fleetbase-backup.log
EOF

sudo chmod +x /opt/fleetbase/backup.sh

# Schedule daily backups
(crontab -l 2>/dev/null; echo "0 2 * * * /opt/fleetbase/backup.sh") | crontab -
```

### Recovery Procedures

```bash
# Database recovery
mysql -u fleetbase -psecure_password fleetbase < /opt/fleetbase/backups/mysql/fleetbase_YYYYMMDD_HHMMSS.sql

# File recovery
tar -xzf /opt/fleetbase/backups/files/storage_YYYYMMDD_HHMMSS.tar.gz -C /

# Configuration recovery
cp /opt/fleetbase/backups/config/env_YYYYMMDD_HHMMSS /var/www/fleetbase/api/.env
cp /opt/fleetbase/backups/config/nginx_YYYYMMDD_HHMMSS /etc/nginx/sites-available/fleetbase
```

## Monitoring and Maintenance

### System Monitoring

```bash
# Install monitoring tools
sudo apt install -y htop iotop nethogs sysstat

# Create monitoring script
sudo cat > /opt/fleetbase/monitor.sh << 'EOF'
#!/bin/bash

LOG_FILE="/var/log/fleetbase-monitor.log"
DATE=$(date)

echo "$DATE - System Monitor Check" >> $LOG_FILE

# Check disk space
DISK_USAGE=$(df -h / | awk 'NR==2 {print $5}' | sed 's/%//')
if [ $DISK_USAGE -gt 80 ]; then
    echo "WARNING: Disk usage is $DISK_USAGE%" >> $LOG_FILE
fi

# Check memory usage
MEM_USAGE=$(free | awk 'NR==2{printf "%.2f", $3/$2*100}')
if [ $(echo "$MEM_USAGE > 80" | bc) -eq 1 ]; then
    echo "WARNING: Memory usage is $MEM_USAGE%" >> $LOG_FILE
fi

# Check services
for service in nginx mysql redis php8.1-fpm; do
    if ! systemctl is-active --quiet $service; then
        echo "ERROR: $service is not running" >> $LOG_FILE
    fi
done

# Check Docker containers (if using Docker)
if command -v docker-compose &> /dev/null; then
    cd /opt/fleetbase
    UNHEALTHY=$(docker-compose ps | grep -v "Up" | wc -l)
    if [ $UNHEALTHY -gt 1 ]; then
        echo "WARNING: $UNHEALTHY containers are not healthy" >> $LOG_FILE
    fi
fi
EOF

sudo chmod +x /opt/fleetbase/monitor.sh

# Schedule monitoring every 15 minutes
(crontab -l 2>/dev/null; echo "*/15 * * * * /opt/fleetbase/monitor.sh") | crontab -
```

### Log Management

```bash
# Configure log rotation
sudo cat > /etc/logrotate.d/fleetbase << 'EOF'
/var/log/fleetbase-*.log {
    daily
    missingok
    rotate 30
    compress
    delaycompress
    notifempty
    create 0644 www-data www-data
}

/var/www/fleetbase/api/storage/logs/*.log {
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

## Troubleshooting

### Common Issues

1. **Service startup failures**:
   ```bash
   # Check service status
   sudo systemctl status nginx mysql redis php8.1-fpm
   
   # Check logs
   sudo journalctl -u nginx -f
   sudo tail -f /var/log/mysql/error.log
   ```

2. **Database connection issues**:
   ```bash
   # Test database connection
   mysql -u fleetbase -p -h localhost fleetbase
   
   # Check MySQL configuration
   sudo mysql -u root -p -e "SHOW VARIABLES LIKE 'bind_address';"
   ```

3. **Permission issues**:
   ```bash
   # Fix file permissions
   sudo chown -R www-data:www-data /var/www/fleetbase
   sudo chmod -R 775 /var/www/fleetbase/api/storage
   ```

4. **SSL certificate issues**:
   ```bash
   # Test SSL certificate
   openssl x509 -in /opt/fleetbase/ssl/fleetbase.crt -text -noout
   
   # Check certificate expiration
   openssl x509 -in /opt/fleetbase/ssl/fleetbase.crt -noout -dates
   ```

### Performance Optimization

```bash
# MySQL optimization
sudo cat >> /etc/mysql/mysql.conf.d/performance.cnf << 'EOF'
[mysqld]
innodb_buffer_pool_size = 2G
innodb_log_file_size = 512M
innodb_flush_log_at_trx_commit = 2
query_cache_type = 1
query_cache_size = 512M
max_connections = 300
innodb_buffer_pool_instances = 4
EOF

# PHP optimization
sudo sed -i 's/memory_limit = 128M/memory_limit = 512M/' /etc/php/8.1/fpm/php.ini
sudo sed -i 's/max_execution_time = 30/max_execution_time = 300/' /etc/php/8.1/fpm/php.ini
sudo sed -i 's/upload_max_filesize = 2M/upload_max_filesize = 100M/' /etc/php/8.1/fpm/php.ini
sudo sed -i 's/post_max_size = 8M/post_max_size = 100M/' /etc/php/8.1/fpm/php.ini

# Redis optimization
sudo cat >> /etc/redis/redis.conf << 'EOF'
maxmemory 1gb
maxmemory-policy allkeys-lru
save 900 1
save 300 10
save 60 10000
EOF

# Restart services
sudo systemctl restart mysql php8.1-fpm redis-server
```

## Conclusion

You now have a comprehensive on-premise Fleetbase deployment with:

- ✅ **Complete infrastructure control** with Docker or native installation
- ✅ **Enterprise security** with SSL, firewalls, and access controls
- ✅ **High availability** options with load balancing and clustering
- ✅ **Automated backups** and recovery procedures
- ✅ **Monitoring and alerting** for proactive maintenance
- ✅ **Performance optimization** for production workloads

Your on-premise deployment ensures data sovereignty, compliance with internal policies, and complete customization capabilities while maintaining enterprise-grade security and reliability.

For additional support and advanced configurations, visit the [Fleetbase documentation](https://docs.fleetbase.io) or join our [Discord community](https://discord.gg/V7RVWRQ2Wm).

