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

## 🧼 Reload a specific pm2

```
pm2 reload pm2-instance-name(project name)
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

# Upload and Extract ZIP on VPS via SCP

This guide explains how to upload a `.zip` file from your local machine to a VPS and extract it in the desired directory.

## 🛠️ Requirements

- SSH access to your VPS
- `scp` installed on your local machine
- `unzip` installed on the VPS (`sudo apt install unzip`)

## 📤 Upload ZIP to VPS

Use the following command from your local terminal:

```bash
scp C:\Users\Asus\Downloads\yourfilename.zip youruser@your-vps-ip:/target/directory/

````markdown
# Laravel Queue Worker Setup on VPS

This guide explains how to configure and run Laravel job queues on a VPS using **Supervisor** for process management.  

---

## 🔹 Step 1: Configure Laravel Queue

### 1.1 Migration for Jobs Table
Run the following to create the `jobs` table:

```bash
php artisan queue:table
php artisan migrate
````

### 1.2 Update `.env`

Set the queue driver to database:

```env
QUEUE_CONNECTION=database
```

> If using Redis, set `QUEUE_CONNECTION=redis` and install Redis.

---

## 🔹 Step 2: Test Queue Locally

Before setting up Supervisor, test that your queue works:

```bash
php artisan queue:work
```

This will start processing jobs in real time. Stop it with `CTRL + C`.

---

## 🔹 Step 3: Install Supervisor

Supervisor is a process monitor that ensures your queue worker keeps running even after crashes or reboots.

Install Supervisor:

```bash
sudo apt update
sudo apt install supervisor -y
```

---

## 🔹 Step 4: Configure Supervisor

Create a config file at `/etc/supervisor/conf.d/laravel-worker.conf`:

```ini
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /var/www/html/your-laravel-project/artisan queue:work --sleep=3 --tries=3 --max-time=3600
autostart=true
autorestart=true
user=www-data
numprocs=1
redirect_stderr=true
stdout_logfile=/var/www/html/your-laravel-project/storage/logs/worker.log
stopwaitsecs=3600
```

> ⚠️ Replace `/var/www/html/your-laravel-project` with the actual path of your Laravel project.

---

## 🔹 Step 5: Enable Supervisor Config

Run the following commands:

```bash
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl start laravel-worker:*
```

---

## 🔹 Step 6: Manage Worker

* Check status:

  ```bash
  sudo supervisorctl status
  ```
* Stop worker:

  ```bash
  sudo supervisorctl stop laravel-worker:*
  ```
* Restart worker:

  ```bash
  sudo supervisorctl restart laravel-worker:*
  ```

---

## 🔹 Step 7: Logs

* Worker logs:

  ```
  storage/logs/worker.log
  ```
* Laravel logs:

  ```
  storage/logs/laravel.log
  ```

---

## 🔹 Optional: Redis + Horizon

If you want real-time monitoring and more control:

```bash
composer require laravel/horizon
php artisan horizon
```

Then replace `queue:work` in Supervisor with:

```bash
php artisan horizon
```

---

## ✅ Summary

1. Configure queue driver (Database/Redis).
2. Test with `php artisan queue:work`.
3. Install Supervisor.
4. Add config at `/etc/supervisor/conf.d/laravel-worker.conf`.
5. Use Supervisor to keep workers running automatically.

This ensures your Laravel jobs are processed **reliably in the background**.

```

---

Do you want me to also include a **sample job class + dispatch example** inside this README so you (or your teammates) can immediately test the queue setup after following the steps?
```
