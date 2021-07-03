
# Deploying in Cloud

A guide for deploying files in cloud and use nginix to serve that files



## Initial Server Setup

First open http and https ports

1. Create new user
```bash
  adduser sammy
  usermod -aG sudo sammy

```

2. Setting up basic firewall
```bash
  ufw app list
  ufw allow OpenSSH
  ufw enable
  ufw status
```

3. Setting up basic firewall
```bash
  ufw app list
  ufw allow OpenSSH
  ufw enable
  ufw status
```

## Nginx Setup

1. Install Nginx

```bash
  sudo apt update
  sudo apt install nginx
``` 
2. Allow Nginx through firewall

```bash
  sudo ufw app list
  sudo ufw allow 'Nginx Full'
  sudo ufw status
``` 
3. Checking Web Server

```bash
  systemctl status nginx
  http://your_server_ip
``` 

4. Setting up server blocks
```bash
  sudo mkdir -p /var/www/your_domain/html
  sudo chown -R $USER:$USER /var/www/your_domain/html
  sudo chmod -R 755 /var/www/your_domain
  nano /var/www/your_domain/html/index.html
  sudo nano /etc/nginx/sites-available/your_domain
```  
5 . Paste the code below
```bash
  server {
        listen 80;
        listen [::]:80;

        root /var/www/your_domain/html;
        index index.html index.htm index.nginx-debian.html;

        server_name your_domain www.your_domain;

        location / {
                try_files $uri $uri/ =404;
        }
  }

```

6. Enable the domain
```bash
  sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
  sudo nginx -t
  sudo systemctl restart nginx
```
Nginx should now be serving your domain name. You can test this by navigating to http://your_domain

## Serving webapps in localhost
Open the application in localhost
```bash
  sudo nano /etc/nginx/sites-available/example.com 
```
Paste the code and change the port if necessery
```bash
  server {
  ...
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
  ...
  }
```

You can add additional location blocks to the same server block to provide access to other applications on the same server. For example, if you were also running another Node.js application on port 3001, you could add this location block to allow access to it via https://example.com/app2

```bash
  server {
  ...
    location /app2 {
        proxy_pass http://localhost:3001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
  ...
  }
```

Restart Nginx
```bash
  sudo nginx -t
  sudo systemctl restart nginx
```

## Authors

- [@anoopaneesh](https://github.com/anoopaneesh)

  