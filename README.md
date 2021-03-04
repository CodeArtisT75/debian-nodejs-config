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
apt install -y curl wget apt-transport-https
```

set password for www-data user:

```bash
passwd www-data
```

set www-data home to /opt:

```bash
usermod -d /opt/ www-data
chmod -R 700 /opt
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