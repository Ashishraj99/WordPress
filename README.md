
# Automated deployment process for a WordPress website
using Nginx as the web server, LEMP (Linux, Nginx, MySQL, PHP) stack, and GitHub
Actions as the CI/CD automation tool.

Step-1) Server Provisioning:

Choose a Cloud Provider: Sign up with a major cloud provider AWS and create a new Virtual Private Server (VPS) instance. Ensure that you select a secure Linux distribution, such as Ubuntu 22.04, during the instance creation.

Connect to the Server: Use SSH to connect to your VPS instance using the provided IP address and SSH key.

Update Packages: Run the following commands to update the package list and upgrade installed packages:

```
sudo apt update
sudo apt upgrade
sudo apt install nginx mysql-server php-fpm php-mysql php-curl php-gd php-mbstring php-xml
```

Step-2) Website Configuration:

1) Set up a WordPress website on the VPS and secure it following best practices (e.g., strong passwords, limited user privileges, etc.).

Configure Nginx: Create an Nginx server for your WordPress site.

```
sudo nano /etc/nginx/sites-available/demoword.com
```

Add the following configuration (adjust paths and settings as needed):

```
server {
    listen 80;
    server_name example.com www.example.com;

    root /var/www/html;
    index index.php;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}

```

Enable the server block and restart Nginx:

```
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx

```

2) Install and Configure MySQL/MariaDB: Follow the prompts to set up a secure MySQL installation:

```
sudo mysql_secure_installation
sudo mysql -u root -p

```
Create a new MySQL database and user for your WordPress site:

```
CREATE DATABASE wordpress;
CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpressuser'@'localhost';
FLUSH PRIVILEGES;
Exit

```

3) Install WordPress: Download and extract WordPress to the web root directory:

```
cd /var/www/html
sudo wget https://wordpress.org/latest.tar.gz
sudo tar -xzvf latest.tar.gz
sudo mv wordpress/* ./
sudo rm -rf wordpress latest.tar.gz

```

4) Configure WordPress: Copy the sample configuration file and update the settings:

```
cp wp-config-sample.php wp-config.php
sudo nano wp-config.php

```
Replace the database settings with the ones you created earlier.

5) Secure WordPress: Set appropriate permissions on WordPress files and directories:

```
sudo chown -R www-data:www-data /var/www/html
sudo find /var/www/html -type d -exec chmod 755 {} \;
sudo find /var/www/html -type f -exec chmod 644 {} \;

```

Step-3) SSL/TLS Certificate

1) Install Certbot: Install Certbot to obtain and manage Let's Encrypt certificates:

```
sudo apt install certbot python3-certbot-nginx -y

```

2) Obtain SSL Certificate: Use Certbot to obtain a free SSL certificate for your domain:

```
sudo certbot --nginx -d demoword.com -d www.demoword.com

```

Step-4) Optimize Nginx Configuration

1) Enable Gzip Compression: Edit your Nginx configuration file and add the following lines within the http block:

```
gzip on;
gzip_types application/javascript image/* text/css;

```

2) Enable Browser Caching: Add the following lines to enable browser caching:

```
location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
    expires max;
    log_not_found off;
}

```
3) After making these changes, test and reload Nginx:

```
sudo nginx -t
sudo systemctl reload nginx

```





