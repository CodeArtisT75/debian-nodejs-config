# Default config for Debian 10 server (Nodejs + Nginx)

## Server preparation:

Run these commands (as SU) to update all dependencies and then reboot:

```bash
apt update
apt upgrade
apt autoremove
apt autoclean

reboot
```

install these packages:

```bash
apt install -y curl wget apt-transport-https git
```

create user for your project:

```bash
adduser APP_USER
```

## Installing NodeJS:

run these commands to install nodejs:

```bash
cd ~
curl -sL https://deb.nodesource.com/setup_14.x -o nodesource_setup.sh
bash nodesource_setup.sh

apt install nodejs

rm nodesource_setup.sh
```

###### if you want to install another version just change 14.x to the version you want.


## Installing Nginx:

run this command:

```bash
apt install nginx
```

## Installing MySQL:

run this commands to add MySQL Repo:

```bash
wget http://repo.mysql.com/mysql-apt-config_0.8.14-1_all.deb

apt update
```

run this command to install MySQL:

```bash
apt install -y mysql-server
```

after that, run this command to configure it:

```bash
mysql_secure_installation
```

## Installing MongoDB:

run this command to install MongoDB:

```bash
wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
echo "deb http://repo.mongodb.org/apt/debian buster/mongodb-org/4.4 main" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list

apt update

apt install mongodb-org
```

run this command to set password for root:

```bash
mongo admin --eval 'db.createUser({ user:"root", pwd:"YOUR_PASSWORD", roles:[{role:"root", db:"admin"}]})'
```

after that go to this file:

```bash
nano /etc/mongodb.conf
```

and set `auth` to `true`:

```bash
auth=true
```

finally restart mongodb service

```bash
systemctl restart mongodb
```

## Installing Redis:

run this command:

```bash
apt install redis-server
```

to set password for redis, go to this file:

```bash
nano /etc/redis/redis.conf
```

uncomment `requirepass` and fill it with your password:

```bash
requirepass YOU_PASSWORD
```

## Installing PM2:

run this command to install PM2:

```bash
npm install -g pm2
```

## Installing Yarn:

run this command to install yarn:

```bash
npm install -g yarn
```

## Deploying project on server:

### before uploading files:

remember you have to create a MySQL database for your project ***(with superuser)***.

run this command with SU:

```bash
mysql
```

and run these commands to create a database and a user for it:

```SQL
CREATE DATABASE YOUR_DB_NAME COLLATE utf8_general_ci;
CREATE USER 'YOUR_DB_USERNAME'@'localhost' IDENTIFIED BY 'YOUR_DB_PASSWORD';
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, CREATE TEMPORARY TABLES, SHOW VIEW ON YOUR_DB_NAME.* TO 'YOUR_DB_USERNAME'@'localhost';
FLUSH PRIVILEGES;
```

if you want to proxy-forward your app with Nginx, copy these command in `/etc/nginx/sites-available/default`:

```bash
server {
  listen 80;

  server_name myapp.com;

  location / {
      proxy_pass http://localhost:3000;
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

###### port = 3000 | domain = myapp.com

### Now you can upload your files

login with your project user.

first of all upload your files (or pull from git):

```bash
git pull YOUR_REPOSITORY_ADDRESS
```

then install packages with your package manager (npm or yarn):

```bash
npm install
yarn install
```

make your public directories Public :) :

```bash
chmod -R 700 /YOUR_PROJECT_PUBLIC_DIRECTORY
```

set your ENVIROMENT variables (mostly in .env file):

```bash
nano .env
```

run your app with PM2:

```bash
pm2 start app.js --name your_app_name
```
