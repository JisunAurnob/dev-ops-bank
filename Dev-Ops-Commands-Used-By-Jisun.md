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
