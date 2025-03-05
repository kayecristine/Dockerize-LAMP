# Dockerizing a LAMP Stack

## Prerequisites
Ensure you have the following installed on your Windows machine:

* Docker Desktop (running in WSL2 mode)
* PowerShell

## Step 1: Create a Project Directory
Open PowerShell and navigate to the location where you want to store your project. Run:

```powershell
mkdir lamp_docker
cd lamp_docker
```

## Step 2: Create Required Files
2.1 Create docker-compose.yml
Run the following command to create the file:

```powershell
New-Item -Path . -Name "docker-compose.yml" -ItemType "file"
```

Then, open it in Notepad or any text editor and paste the following content:

```yaml
version: '3.8'

services:
  lamp_php:
    build: .
    container_name: lamp_php
    volumes:
      - ./src:/var/www/html
    networks:
      - lamp_network

  lamp_mysql:
    image: mysql:8.0
    container_name: lamp_mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: test_db
      MYSQL_USER: user
      MYSQL_PASSWORD: userpassword
    volumes:
      - ./mysql:/docker-entrypoint-initdb.d
    networks:
      - lamp_network

  lamp_nginx:
    image: nginx:latest
    container_name: lamp_nginx
    ports:
      - "8080:80"
    volumes:
      - ./src:/var/www/html
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - lamp_php
    networks:
      - lamp_network

  lamp_phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: lamp_phpmyadmin
    restart: always
    ports:
      - "8081:80"
    environment:
      PMA_HOST: lamp_mysql
      MYSQL_ROOT_PASSWORD: rootpassword
    depends_on:
      - lamp_mysql
    networks:
      - lamp_network

networks:
  lamp_network:
```
Save and close the file.

2.2 Create a Dockerfile for PHP

```powershell
New-Item -Path . -Name "Dockerfile" -ItemType "file"
```
Paste the following content:

```dockerfile
FROM php:8.2-fpm-alpine

RUN docker-php-ext-install mysqli pdo pdo_mysql

WORKDIR /var/www/html

COPY src/ /var/www/html
```
Save and close the file.

2.3 Create an nginx Folder and Configuration File

```powershell
mkdir nginx
New-Item -Path "./nginx" -Name "default.conf" -ItemType "file"
```
Paste the following content into `nginx/default.conf`:

```nginx
server {
    listen 80;
    server_name localhost;

    root /var/www/html;
    index index.php index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass lamp_php:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME /var/www/html$fastcgi_script_name;
    }
}
```
Save and close the file.

2.4 Create the `src` Folder and an `index.php` File

```powershell
mkdir src
New-Item -Path "./src" -Name "index.php" -ItemType "file"
```
Paste the following content into `src/index.php`:

```php
<?php
phpinfo();
?>
```
Save and close the file.

2.4 Create the `mysql` Folder and an `init.sql` File

```powershell
mkdir mysql
New-Item -Path "./mysql" -Name "init.sql" -ItemType "file"
```
Paste the following content into `mysql\init.sql`:

```php
CREATE DATABASE IF NOT EXISTS mydatabase;

USE mydatabase;

CREATE TABLE IF NOT EXISTS users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO users (name, email) VALUES
('Alice', 'alice@example.com'),
('Bob', 'bob@example.com');
```
Save and close the file.
## Step 3: Build and Run the Containers
Go to your project folder in PowerShell and run:

```powershell
docker-compose up -d --build
```

This will:
* ✅ Build the PHP container
* ✅ Pull MySQL, Nginx, and phpMyAdmin images
* ✅ Start all services in detached mode

## Step 4: Verify the Setup
4.1 Check Running Containers
```powershell
docker ps
```

You should see `lamp_nginx`, `lamp_php`, `lamp_mysql`, and `lamp_phpmyadmin` running.

4.2 Open in Browser
* PHP Info Page: http://localhost:8080
* phpMyAdmin: http://localhost:8081

Log in to phpMyAdmin

* Server:	lamp_mysql or localhost
* Username:	root or user
* Password:	rootpassword (for root) or userpassword (for user)

## Step 5: Stopping and Restarting the Containers
To Stop the Containers
```powershell
docker-compose down
```
To Restart the Containers
```powershell
docker-compose up -d
```
## Step 6: Cleanup (Stopping and Removing Containers & Volumes)
After testing your LAMP stack, you may want to clean up your environment to free up system resources. Run the following commands in PowerShell:

Stop and remove all containers:

```powershell
docker-compose down
```

This stops and removes all running containers for the LAMP stack.

Remove unused Docker images (optional):

```powershell
docker image prune -a
```
This deletes all unused images to free up disk space.

Remove unused volumes (optional):

```powershell
docker volume prune
```
This removes any Docker volumes not in use by active containers.
