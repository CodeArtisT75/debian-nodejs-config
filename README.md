# Default Configuration for Debian Server (Node.js + Nginx)

## Server Preparation

Run these commands (as root) to update all dependencies and reboot:

```bash
apt update && apt upgrade -y
apt autoremove -y
apt autoclean -y
reboot
```

Install required packages:

```bash
apt install -y curl wget apt-transport-https git
```

Create a user for your project:

```bash
adduser APP_USER
```

---

## Installing Node.js

To install Node.js using NVM, run:

```bash
cd ~
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

# Use the desired Node.js version
nvm use 22
```

> **Tip:** If you need another version, replace `22` with the required version number.

---

## Installing Nginx

```bash
apt install -y nginx
```

---

## Installing MySQL

Add the MySQL repository and install MySQL:

```bash
wget http://repo.mysql.com/mysql-apt-config_0.8.14-1_all.deb
apt update
apt install -y mysql-server
```

Run the MySQL secure installation wizard:

```bash
mysql_secure_installation
```

Create a database and a user:

```bash
mysql
```

Run the following commands inside MySQL:

```sql
CREATE DATABASE YOUR_DB_NAME COLLATE utf8_general_ci;
CREATE USER 'YOUR_DB_USERNAME'@'localhost' IDENTIFIED BY 'YOUR_DB_PASSWORD';
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, CREATE TEMPORARY TABLES, SHOW VIEW ON YOUR_DB_NAME.* TO 'YOUR_DB_USERNAME'@'localhost';
FLUSH PRIVILEGES;
```

---

## Installing PostgreSQL

Install PostgreSQL:

```bash
apt install -y postgresql postgresql-contrib
```

Ensure the service is running:

```bash
systemctl start postgresql
```

Create a PostgreSQL user and database:

```bash
sudo -u postgres createuser --interactive
sudo -u postgres createdb DB_NAME
```

---

## Installing Redis

```bash
apt install -y redis-server
```

Set a Redis password:

```bash
nano /etc/redis/redis.conf
```

Uncomment `requirepass` and set your password:

```conf
requirepass YOUR_PASSWORD
```

Restart Redis:

```bash
systemctl restart redis
```

---

## Installing PM2

```bash
npm install -g pm2
```

---

## Installing Yarn

```bash
npm install -g yarn
```

---

## Deploying the Project on the Server

### Pre-Deployment Steps

- Create a MySQL or PostgreSQL database for your project (**with superuser permissions**).
- If using Nginx for proxy forwarding, update the default configuration file:

```bash
nano /etc/nginx/sites-available/default
```

Add the following configuration:

```nginx
server {
  listen 80;
  server_name myapp.com; # Replace with your domain

  location / {
      proxy_pass http://localhost:3000; # Change to your app's port
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection 'upgrade';
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-Proto $scheme;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_cache_bypass $http_upgrade;
  }
}
```

### Enhancing Security: Disable Server Signature

1. Install `nginx-extras`:
   ```bash
   apt install -y nginx-extras
   ```
2. Edit Nginx configuration:
   ```bash
   nano /etc/nginx/nginx.conf
   ```
   Add the following:
   ```nginx
   http {
       more_set_headers "Server: Your_New_Server_Name";
       server_tokens off;
   }
   ```
3. Restart Nginx:
   ```bash
   systemctl restart nginx
   ```

---

## Uploading Project Files

Log in with the project user and pull the latest files from the repository:

```bash
git pull YOUR_REPOSITORY_ADDRESS
```

Install dependencies using your package manager:

```bash
npm install
# or
yarn install
```

Set environment variables (typically in a `.env` file):

```bash
nano .env
```

Run the application with PM2:

```bash
pm install -g pm2
pm2 start app.js --name your_app_name
```

Or using Docker Compose:

```bash
docker compose --profile profile_name up -d --build
```

---

### Notes

- Default application port: `3000`
- Default domain: `myapp.com`

This guide ensures a robust, production-ready setup for your Node.js application on a Debian server. 🚀
