---
title: Install from Source
sidebar_position: 3
slug: /getting-started/install/from-source
---

If you'd like to install and setup Fleetbase without Docker you will need to properly prepare your server first with dependencies. Fleetbase is built with PHP and MySQL.

## Setup on Debian or Ubuntu

### 1. Install Dependencies
```bash
sudo apt-get update && apt-get install -y git bind9-utils mycli nodejs npm php php-cli php-fpm git curl build-essential
```

### 2. Install Composer
```bash
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php composer-setup.php
sudo mv composer.phar /usr/local/bin/composer
```

### 3. Install PNPM & Ember.js CLI
```bash
npm i -g pnpm ember-cli
```

### 4. Install FrankenPHP

First, find out the architecture of your Debian system. You can do this with the following command:
```bash
uname -m
```
- If the output is something like x86_64, amd64, or similar, then your system is a 64-bit Intel or AMD processor. 
- If the output is aarch64 or similar, then your system is ARM 64-bit.

#### Install for aarch64
```bash
# Download the FrankenPHP Binary Build
wget https://github.com/dunglas/frankenphp/releases/download/v1.1.5/frankenphp-linux-aarch64
# Make the Binary Executable
chmod +x frankenphp-linux-aarch64
# Move the Binary to a System Path
sudo mv frankenphp-linux-aarch64 /usr/local/bin/frankenphp
# Test the Installation
frankenphp --version
```

#### Install for x86_64, amd64, or similar
```bash
# Download the FrankenPHP Binary Build
wget https://github.com/dunglas/frankenphp/releases/download/v1.1.5/frankenphp-linux-x86_64
# Make the Binary Executable
chmod +x frankenphp-linux-x86_64
# Move the Binary to a System Path
sudo mv frankenphp-linux-x86_64 /usr/local/bin/frankenphp
# Test the Installation
frankenphp --version
```

### 5. Install SocketCluster

SocketCluster is a Node.js based server that enables real-time, bidirectional and event-based communication between clients and the server. Fleetbase uses SocketCluster to enable real-time events and activities such as vehicle tracking.

#### Step 1: Install SocketCluster
Create a directory for your SocketCluster server:
It's a good practice to keep your applications organized in separate directories.
```
mkdir ~/socketcluster
cd ~/socketcluster
```

- Install SocketCluster:
You can install SocketCluster globally or in your project directory. For a system-wide installation, use:
```bash
npm install -g socketcluster
```

For a local installation, you can initialize a new npm project and then install SocketCluster locally within it:
```bash
npm init -y
npm install socketcluster
```

#### Step 2: Set Up Your SocketCluster Server

1. Create a basic server file:
Let's create a simple SocketCluster server file. If you installed SocketCluster locally, your project directory is already set. Create a new file named server.js:
```bash
nano server.js
```

Add the following basic server setup:

```javascript
const SocketCluster = require('socketcluster');

const socketCluster = new SocketCluster({
  workers: 1, // Number of worker processes
  brokers: 1, // Number of broker processes
  port: 8001, // Port number for your SocketCluster
});

socketCluster.on('workerMessage', function (workerId, message, respond) {
  console.log('Received message from worker ' + workerId + ': ', message);
  respond(null, 'This is a response from master');
});
```

This script initializes a SocketCluster server with basic configuration.

2. Run your SocketCluster server:

Start your server using Node.js:
```bash
node server.js
```

#### Step 3: Configure SocketCluster as a System Service

To ensure your SocketCluster server runs continuously and starts at boot, you can set it up as a systemd service.

1. Create a systemd service file:
Create a new service file in /etc/systemd/system/:
```bash
sudo nano /etc/systemd/system/socketcluster.service
```

Add the following configuration:

```ini
[Unit]
Description=SocketCluster server
After=network.target

[Service]
User=root
WorkingDirectory=/home/root/socketcluster
ExecStart=/usr/bin/node server.js
Restart=always

[Install]
WantedBy=multi-user.target
```

Replace `root` with your actual username.

2. Reload systemd and start the service:
Enable and start your new service:
```bash
sudo systemctl daemon-reload
sudo systemctl start socketcluster.service
sudo systemctl enable socketcluster.service
```

3. Check the status of your service:
To ensure that your service is running correctly:
```bash
sudo systemctl status socketcluster.service
```

#### Conclusion
You now have a basic SocketCluster server set up on your Debian system. It's configured to start automatically at boot, and you can customize your server.js to fit the specific requirements of your Fleetbase setup.

### 6. Install MySQL

#### Step 1: Add the MySQL APT Repository

MySQL 8 might not be available directly from the default Debian or Ubuntu repositories, so you generally need to add the official MySQL APT repository to get the latest version.

1. Download the MySQL APT repository config tool:
```bash
wget https://dev.mysql.com/get/mysql-apt-config_0.8.22-1_all.deb
```

2. Install the repository package:
```bash
sudo dpkg -i mysql-apt-config_0.8.22-1_all.deb
```
During the installation, a prompt will appear allowing you to select the version of MySQL you wish to install. Make sure to select MySQL 8.0.

3. Update your package lists to reflect the new repository:
```bash
sudo apt update
```

#### Step 2: Install MySQL 8

1. Install MySQL
```bash
sudo apt install mysql-server
```

2. During the installation, you will be prompted to create a root password. Choose a secure password and complete the setup.

#### Step 3: Secure MySQL Installation

After installing, it's recommended to run the security script that comes with MySQL:
```bash
sudo mysql_secure_installation
```

This script will guide you through some settings that improve MySQL security, such as removing anonymous users and the test database.

### 7. Install Redis

#### Step 1: Install Redis

Redis is available from the default repositories on both Debian and Ubuntu:
```bash
sudo apt update
sudo apt install redis-server
```

#### Step 2: Configure Redis

By default, Redis is not configured for production use. You can modify its configuration file to suit your needs:
```bash
sudo nano /etc/redis/redis.conf
```

Here are a few common settings to consider:

- **Supervised directive:** Set it to systemd if you are using systemd to manage services. This allows better management and logging through systemd.
```bash
supervised systemd
```

- **Binding to localhost:** Ensure Redis is only listening on localhost unless needed otherwise, for security reasons.
```bash
bind 127.0.0.1 ::1
```

- **Setting a password:** Uncomment and set the requirepass directive to secure Redis with a password.
```bash
requirepass yourverysecurepassword
```

#### Step 3: Restart and Enable Redis Service

After configuring Redis, restart the service to apply changes:
```bash
sudo systemctl restart redis.service
```

To ensure Redis starts on boot, enable it:
```bash
sudo systemctl enable redis.service
```

### 8. Clone Fleetbase
```bash
git clone git@github.com:fleetbase/fleetbase.git /fleetbase
cd /fleetbase
```

### 9. Configure Fleetbase API

To successfully set up Fleetbase, you need to configure various settings through environment variables. These variables will enable you to tailor the behavior of the Fleetbase system to fit your deployment environment. You can read more about configuration in the [configuration section](/getting-started/configuration) of this guide.

#### Step 1: Access the Configuration File

Open the `.env` file located in the `api` directory. You can use any text editor; the example below uses `nano`:

```bash
nano api/.env
```

#### Step 2: Configure Essential Variables

In the .env file, add the following environment variables. These variables are essential for the basic operation of the Fleetbase server:

- `APP_URL`: The URL where your Fleetbase API is accessible. For development, you might use localhost.
- `CONSOLE_HOST`: The URL for the Fleetbase console. Required for CORS configuration.
- `DATABASE_URL`: The full database connection URL, including username, password, host, and database name.
- `STOREFRONT_DB_CONNECTION`: The database connection name for the storefront.
- `QUEUE_CONNECTION`: Defines the queue backend, which in this case is Redis.
- `CACHE_DRIVER`: Specifies Redis as the caching backend.
- `CACHE_PATH`: The file path for storing cache files, useful if file-based caching is used.
- `CACHE_URL`: The URL for the Redis cache server.
- `REDIS_URL`: The URL for the Redis server used for caching and queues.
- `SESSION_DOMAIN`: The domain under which sessions are valid.
- `BROADCAST_DRIVER`: The technology used for broadcasting real-time messages.
- `MAIL_FROM_NAME`: Default name used in emails sent by the system.
- `APP_NAME`: The name of your application.
- `LOG_CHANNEL`: Specifies how application logging is handled.
- `MODEL_CACHE_ENABLED`: Toggle to enable/disable model caching.
- `RESPONSE_CACHE_ENABLED`: Toggle to enable/disable response caching.
- `RESPONSE_CACHE_DRIVER`: Specifies the caching driver for responses.
- `MAIL_MAILER`: Defines the mail backend, for example: Sendgrid, SES (Amazon Simple Email Service), Postmark.
- `IPINFO_API_KEY`: API key for IPInfo service, if used.
- `GOOGLE_MAPS_API_KEY`: API key for Google Maps services.
- `GOOGLE_MAPS_LOCALE`: Locale setting for Google Maps.
- `TWILIO_SID`: Account SID for Twilio, used for sending SMS.
- `TWILIO_TOKEN`: Authentication token for Twilio.
- `TWILIO_FROM`: The phone number from which Twilio SMS are sent.
- `SENTRY_LARAVEL_DSN`: The DSN for Sentry, used for error tracking.
- `OSRM_HOST`: The OSRM server to use, defaults to https://router.project-osrm.org

#### Example Configuration
Below is an example configuration for a development environment:
```bash
APP_URL=http://localhost:8000
CONSOLE_HOST=http://localhost:4200
OSRM_HOST=https://router.project-osrm.org
DATABASE_URL=mysql://root:password@localhost/fleetbase
STOREFRONT_DB_CONNECTION=storefront
QUEUE_CONNECTION=redis
CACHE_DRIVER=redis
CACHE_PATH=/path/to/fleetbase/api/storage/framework/cache
CACHE_URL=redis://localhost:6379
REDIS_URL=redis://localhost:6379
SESSION_DOMAIN=localhost
BROADCAST_DRIVER=socketcluster
MAIL_FROM_NAME=Fleetbase
APP_NAME=Fleetbase
LOG_CHANNEL=daily
MODEL_CACHE_ENABLED=true
RESPONSE_CACHE_ENABLED=true
RESPONSE_CACHE_DRIVER=redis
MAIL_MAILER=ses
IPINFO_API_KEY=your_ipinfo_api_key
GOOGLE_MAPS_API_KEY=your_google_maps_api_key
GOOGLE_MAPS_LOCALE=us
TWILIO_SID=your_twilio_sid
TWILIO_TOKEN=your_twilio_token
TWILIO_FROM=your_twilio_from_number
SENTRY_LARAVEL_DSN=your_sentry_dsn
```

#### Step 3: Save and Close
After entering the necessary variables, save and close the file. If you're using nano, you can do this by pressing CTRL + X, then Y to confirm, and Enter to exit.

#### Step 4: Validate Configuration
Ensure all services mentioned in the .env file are properly installed and running, such as Redis, MySQL, and any external services like Twilio or Sentry. This validation will help you catch any misconfigurations or service interruptions before going live.

### 10. Configure Fleetbase Console

Configuring the frontend environment of Fleetbase involves setting various parameters that ensure the frontend communicates correctly with the backend services, such as the API and real-time services like SocketCluster, as well as external services like OSRM.

#### Step 1: Access the Configuration File

Navigate to the `environments` directory of the console and open the `.env.production` file. You can use any text editor; below we use `nano`:

```bash
nano /console/environments/.env.production
```

#### Step 2: Set Environment Variables
Below is an explanation of each variable in the .env.production file. Update each value according to your environment specifics:

- `API_HOST`: The URL where your Fleetbase API is accessible, excluding any path or schema.
- `API_NAMESPACE`: The namespace for the API, typically indicating the version and scope of the API.
- `API_SECURE`: Indicates whether HTTPS is used to secure API communication. Set to true for HTTPS.
- `SOCKETCLUSTER_PATH`: The path component of the URL where the SocketCluster server can be accessed.
- `SOCKETCLUSTER_HOST`: The hostname or IP address of the SocketCluster server.
- `SOCKETCLUSTER_SECURE`: Specifies whether the SocketCluster connection should use SSL (true for wss://, false for ws://).
- `SOCKETCLUSTER_PORT`: The port on which the SocketCluster server is listening.
- `OSRM_HOST`: The primary URL for the Open Source Routing Machine (OSRM) server.
- `OSRM_SERVERS`: A comma-separated list of URLs for fallback or additional OSRM servers.

#### Example Configuration
Below is an example configuration for a production environment:
```bash
API_HOST=http://localhost:8000
API_NAMESPACE=int/v1
API_SECURE=true
SOCKETCLUSTER_PATH=/socketcluster/
SOCKETCLUSTER_HOST=localhost
SOCKETCLUSTER_SECURE=true
SOCKETCLUSTER_PORT=38000
OSRM_HOST=https://router.project-osrm.org
OSRM_SERVERS=https://canada.routing.fleetbase.io,https://us.routing.fleetbase.io
```

#### Step 3: Save and Close
After configuring the environment variables, save and exit the editor. If you're using nano, this is done by pressing CTRL + X, then pressing Y to confirm the changes, and Enter to exit.

#### Step 4: Validate and Deploy
After configuring the .env.production file, ensure to validate the settings by testing the frontend connectivity with the backend and other services. Check that all endpoints are reachable and functioning as expected. This step is crucial to prevent runtime issues in a production environment.

### 11. Install and Deploy Fleetbase API

#### Step 1: Prepare the Environment

Navigate to the Fleetbase API directory and ensure your environment is set up for the installation:
```bash
cd /fleetbase/api
```

#### Step 2: Install Dependencies

Use Composer to install the necessary PHP dependencies. The `--no-scripts` flag avoids script execution during installation, `--optimize-autoloader` helps with optimizing the autoloader, and `--no-dev` prevents development packages from being installed in a production environment:

```bash
composer install --no-scripts --optimize-autoloader --no-dev
composer dumpautoload  # Optimize the autoloader after installation
```

#### Step 3: Set Permissions

Proper file permissions are critical for the security and proper functioning of Fleetbase:

```bash
chmod -R 755 /fleetbase/api/storage  # Set the correct permissions for the storage directory
chmod +x /fleetbase/api/deploy.sh    # Make the deployment script executable
```

#### Step 4: Run Deployment Script

Execute the deployment script which should handle specific deployment tasks:
```bash
sh deploy.sh
```

#### Step 5: Start the FrankenPHP Server

Launch the FrankenPHP server with the necessary options to serve the Fleetbase API:
```bash
php artisan octane:frankenphp --port=8000 --host=0.0.0.0 --https --http-redirect --caddyfile /fleetbase/Caddyfile
```

This command starts the FrankenPHP server listening on all network interfaces (0.0.0.0), on port 8000, with HTTPS enabled, and uses the specified Caddyfile for server configuration.

### 12. Install and Build Console

#### Step 1: Navigate to the Console Directory

Change to the directory containing the Fleetbase console code:
```bash
cd /fleetbase/console
```

#### Step 2: Install Frontend Dependencies

Use pnpm to install all necessary dependencies. pnpm is efficient and fast which makes it ideal for managing node packages:
```bash
pnpm install
```

#### Step 3: Build the Frontend

Compile and build the frontend assets for production. This step generates the static files needed for deployment:
```bash
pnpm build --environment production
```

### 13. Set Up NGINX for the Console

#### Step 1: Install NGINX

Install NGINX which will serve as the web server for the Fleetbase console:
```bash
sudo apt install nginx
```

#### Step 2: Configure Firewall

Allow HTTP and HTTPS traffic through the firewall:
```bash
sudo ufw allow 'Nginx Full'
```

#### Step 3: Check NGINX Status

Ensure that NGINX is active and running:
```bash
sudo systemctl status nginx
```

#### Step 4: Deploy NGINX Configuration

Copy the custom NGINX configuration file for the Fleetbase console to the appropriate directory and overwrite the default configuration:
```bash
sudo cp nginx.conf /etc/nginx/conf.d/default.conf
```

Additionally if you are setting up for production you might want to modify the default console port of 4200 to 80.

#### Step 5: Reload NGINX

Apply the new configuration by reloading NGINX:
```bash
sudo systemctl reload nginx
```

### Conclusion

Following these steps, you have successfully installed and deployed both the Fleetbase API and Console. The API server is powered by FrankenPHP for efficient handling of requests, and the console is set up with NGINX as the web server to deliver the user interface securely and efficiently.

You should now be able to access Fleetbase on your servers port 4200.