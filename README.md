# Deploy Laravel (VPS)

## 1. Cài đặt LEMP trên VPS

Step 1 – Installing the Nginx Web Server
	
```
sudo apt update
sudo apt install nginx
```

Step 2 — Installing MySQL
  
```
sudo apt install mysql-server

sudo mysql_secure_installation

PRESS Y  ALL AND 1 FOR PASSWORD
  
```
	
Step 3 – Installing PHP
  
```
sudo apt install php-fpm php-mysql
sudo apt-get install -y php-mbstring
sudo apt install php-xml
sudo apt-get install php7.4-zip
```
Step 4 — Configuring Nginx to Use the PHP Processor
  
```
sudo mkdir /var/www/tradecoinpro.trandinhvan.com

sudo chown -R $USER:$USER /var/www/tradecoin.trandinhvan.com

sudo nano /etc/nginx/sites-available/tradecoinpro.trandinhvan.com

```
  Document: 

	https://laravel.com/docs/8.x/deployment
  
  ex:
  
 ```
server {
  listen 80;
  server_name tradecoin.trandinhvan.com;
  root /var/www/tradecoin.trandinhvan.com/public;

  add_header X-Frame-Options "SAMEORIGIN";
  add_header X-Content-Type-Options "nosniff";

  index index.php;

  charset utf-8;

  location / {
      try_files $uri $uri/ /index.php?$query_string;
  }

  location = /favicon.ico { access_log off; log_not_found off; }
  location = /robots.txt  { access_log off; log_not_found off; }

  error_page 404 /index.php;

  location ~ \.php$ {
      fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
      fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
      include fastcgi_params;
  }

  location ~ /\.(?!well-known).* {
      deny all;
  }
}
```

```
sudo ln -s /etc/nginx/sites-available/tradecoinpro.trandinhvan.com /etc/nginx/sites-enabled/

sudo nginx -t

sudo systemctl reload nginx

```

## 2. Cài đặt phpMyadmin

Step 1 — Installing phpMyAdmin
```
sudo apt update

sudo mysql

UNINSTALL COMPONENT "file://component_validate_password";

exit
```

```
sudo apt install phpmyadmin
```

Step 2 — Config Mysql
```
sudo mysql
INSTALL COMPONENT "file://component_validate_password";
CREATE USER 'admin'@'localhost' IDENTIFIED BY 'Pass@123123';
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```
Step 3 — Index phpmyadmin

```
sudo nano /etc/nginx/snippets/phpmyadmin.conf
```

content:

```
location /phpmyadmin {
    root /usr/share/;
    index index.php index.html index.htm;
    location ~ ^/phpmyadmin/(.+\.php)$ {
        try_files $uri =404;
        root /usr/share/;
        fastcgi_pass unix:/run/php/php7.4-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include /etc/nginx/fastcgi_params;
    }

    location ~* ^/phpmyadmin/(.+\.(jpg|jpeg|gif|css|png|js|ico|html|xml|txt))$ {
        root /usr/share/;
    }
}
```

```
sudo nano /etc/nginx/sites-available/tradecoinpro.trandinhvan.com
```

Add to this config:


`include snippets/phpmyadmin.conf;`

```
sudo nginx -t

sudo systemctl reload nginx
```

Check at: http://domain.com/phpmyadmin

## Cài đặt ssl cho website

Step 1 — Installing Certbot

`sudo apt install certbot python3-certbot-nginx`


Step 2 — Obtaining an SSL Certificate

`sudo certbot --nginx -d tradecoinpro.trandinhvan.com`

##Cài đặt Composer

Step 1 — Download and Install Composer
```
cd ~
curl -sS https://getcomposer.org/installer -o composer-setup.php

HASH=`curl -sS https://composer.github.io/installer.sig`

php -r "if (hash_file('SHA384', 'composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"

sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer

composer -v
```

## pull dự án từ git và cấu hình .env

step 1: config ssh

...

Step 2. Git init and add remote 

```
cd /var/www/tradecoin.trandinhvan.com

git init 

git remote add origin git@github.com-bottradecoin:dinhvanit92/bottradecoin.git .
```

Step 2 Pull code
```
git pull origin master
```

Step 3 Config laravel

```
cp .env.example .env
composer install
```

