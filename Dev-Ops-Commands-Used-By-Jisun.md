# Configuration Guide

## Configuring a Domain with Nginx

### Step 1: Create Directory for Subdomain
```bash
sudo mkdir -p /var/www/subdomain.abcd.com
sudo chown -R $USER:$USER /var/www/subdomain.abcd.com
sudo chmod -R 755 /var/www
```

### Step 2: Configure Nginx Sites
Edit the configuration file:
```bash
sudo nano /etc/nginx/sites-available/admin.abcd.com
```

Add the following configuration:
```nginx
server {
    listen 80;
    server_name admin.abcd.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

server {
    listen 80;
    server_name backoffice.abcd.com;

    root /path/to/your/laravel/project/public;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock; # Ensure this matches your PHP version
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}

server {
    listen 80;
    server_name backoffice.abcd.com;

    root /var/www/backoffice.abcd.com;
    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

Save and close the file:
```bash
Ctrl + O -> Enter -> Ctrl + X
```

### Step 3: Enable the Site and Restart Nginx
```bash
sudo ln -s /etc/nginx/sites-available/admin.abcd.com /etc/nginx/sites-enabled/
sudo systemctl restart nginx
sudo systemctl status nginx.service  # Check Nginx status
sudo nginx -t                       # Test Nginx configuration
```

### Additional Configuration
Remove unnecessary sites:
```bash
sudo rm /etc/nginx/sites-enabled/srv633348.hstgr.cloud
```

---

## Using Tmux

### Starting and Managing Tmux Sessions

- **Create a New Tmux Session:**
  ```bash
  tmux new -s myproject
  ```

- **Navigate to Your Project Directory:**
  ```bash
  cd /path/to/your/project
  ```

- **Run Your Project:**
  ```bash
  PORT=3001 npm run start
  ```

- **List Active Tmux Sessions:**
  ```bash
  tmux ls
  ```

- **Attach to an Existing Session:**
  ```bash
  tmux attach-session -t your_session_name
  ```
- **Kill an Existing Session:**
  ```bash
  tmux kill-session -t <session-name>
  ```
- **Create a New Window (Tab):**
While inside the tmux session, press: 
  Press `Ctrl + B`, then `C`.

- **Switch Between Windows**
To move between windows (tabs):

Next window: Press `Ctrl + B`, then `N`.
Previous window: Press `Ctrl + B`, then `P`.
Select a specific window by number: Press `Ctrl + B`, then the `window number`.

- **Detach from a Session (Keep Running):**
  Press `Ctrl + B`, then `D`.

- **Terminate a Session:**
  Press `Ctrl + C`.

---

## Free SSL with Certbot

### Install Certbot
```bash
sudo apt install certbot python3-certbot-nginx
```

### Obtain an SSL Certificate
```bash
sudo certbot --nginx
```

### Test Auto-Renewal
```bash
sudo certbot renew --dry-run
```

---

## MySQL Installation

### Install MySQL Server
```bash
sudo apt install mysql-server
sudo mysql_secure_installation
```

### Install PHP and MySQL Module
```bash
sudo apt install php8.1-fpm php-mysql
```

---

## Other Useful Commands

### Install Build Essentials
```bash
sudo apt-get install build-essential
```

# Deployment Guide for Next.js with Docker

## Docker Commands

### List Docker Images
```sh
docker images
```

### Remove Unused/Dangling Images
```sh
docker image prune -a
```

### List Running Containers
```sh
docker ps
```

### View Logs of Running Container
```sh
docker logs next-app
```

### Build Docker Image
```sh
docker build -t otonest-frontend .
```

### Stop the Running Container
```sh
docker stop next-app
```

### Remove Docker Container
```sh
docker rm next-app
```

### Remove Unused Image
```sh
docker rmi <Image ID>
```

### Run Docker Container
```sh
docker run -d -p 3003:3003 --name next-app otonest-frontend
```

---

## After Pulling from Git
```sh
cd ~/project-directory
git pull origin main
docker build -t otonest-frontend .
docker stop next-app
docker rm next-app
docker run -d -p 3003:3003 --name next-app otonest-frontend
docker ps
docker logs next-app
```

### Notes
- Ensure your Dockerfile is correctly configured before building the image.
- Run `docker logs next-app` to check if the container is running properly.
- Use `docker ps -a` to view all containers, including stopped ones.
- Clean up unused images periodically with `docker image prune -a` to save space.

# PM2 Process Manager – Common Commands Guide

[PM2](https://pm2.keymetrics.io/) is a powerful production process manager for Node.js applications that allows you to keep apps alive forever and reload them without downtime.

---

## 🚀 Installation

```
npm install -g pm2
```

---

## ⚙️ Running an App on a Custom Port

To run your app on a **custom port** (e.g., `PORT=5000`), use:

```
PORT=5000 pm2 start app.js --name my-app
```

> Replace `app.js` with your app's entry file.

---

## 📦 Common PM2 Commands

### ▶️ Start an App

```
pm2 start app.js --name my-app
```

### ♻️ Restart an App

```
pm2 restart my-app
```

### ⏹ Stop an App

```
pm2 stop my-app
```

### ❌ Delete an App

```
pm2 delete my-app
```

### 📋 List All Processes

```
pm2 list
```

### 🔍 View Logs

```
pm2 logs my-app
```

Or all logs:

```
pm2 logs
```

### 📈 Monitor Performance (CPU/Memory)

```
pm2 monit
```

---

## 💾 Saving and Auto-Restart on Reboot

### Save current process list:

```
pm2 save
```

### Generate startup script for reboot:

```
pm2 startup
```

Then follow the instructions it prints (copy and run the given command).

---

## 🛠 Update PM2

```
pm2 update
```

---

## 🧼 Reset All PM2 Data

```
pm2 reset all
```

---

## 📄 Helpful Tips

* Use `.env` files with packages like [dotenv](https://www.npmjs.com/package/dotenv) to manage custom ports and other variables.
* You can also run JSON ecosystem files with:

```
pm2 start ecosystem.config.js
```

---

## 📚 Documentation

Official Docs: [https://pm2.keymetrics.io/docs/usage/quick-start/](https://pm2.keymetrics.io/docs/usage/quick-start/)

---


