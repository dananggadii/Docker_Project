# Laravel Development Setup with Docker Compose

### References: 
- [Laravel Development Setup with Docker Compose](https://docs.docker.com/guides/frameworks/laravel/development-setup/)
- [Laravel Production Setup with Docker Compose](https://docs.docker.com/guides/frameworks/laravel/production-setup/)

### Prerequisite 
- Sudah punya project Laravel (bisa hasil laravel new atau composer create-project)
- Sudah terinstall Docker dan Docker Compose (bisa lewat Docker Desktop)

## Buat Dockerfile

```Dockerfile
# Tentuin versi PHP nya
FROM php:8.2-fpm

# Install dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    libpng-dev \
    libjpeg-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip \
    git \
    curl \
    libzip-dev \
    && docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd zip

# Install Composer (tentuin versi komposer nya)
COPY --from=composer:2.6 /usr/bin/composer /usr/bin/composer

# Set working directory
WORKDIR /var/www

# Copy existing application
COPY . .

# Set permissions
RUN chown -R www-data:www-data /var/www \
    && chmod -R 755 /var/www

# Expose port (tentuin versi port)
EXPOSE 9000

# Start PHP-FPM server
CMD ["php-fpm"]
```

## Buat docker-compose.yml

> - Tentuin `container_name` di services nya
> - Tentuin `container_name` di DB nya
> - Tentuin `environment` di DB nya kaya port, user, password sama database nya
> - Tentuin `container_name`

```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: laravel_app
    restart: unless-stopped
    working_dir: /var/www
    volumes:
      - ./:/var/www
    networks:
      - laravel

  webserver:
    image: nginx:alpine
    container_name: nginx_server
    restart: unless-stopped
    ports:
      - "8000:80"
    volumes:
      - ./:/var/www
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - app
    networks:
      - laravel

  db:
    image: mysql:8.0
    container_name: mysql_db
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: laravel
      MYSQL_USER: laravel
      MYSQL_PASSWORD: secret
    ports:
      - "3306:3306"
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - laravel

volumes:
  db_data:

networks:
  laravel:
    driver: bridge
```

## Buat nginx.conf

```nginx
server {
    listen 80;
    index index.php index.html;
    server_name localhost;
    root /var/www/public;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass app:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

## Menjalankan Laravel di Docker

```bash
docker-compose up -d --build
```
Akses Laravel via browser di:
```bash
http://localhost:8000
``` 

## Setup File .env

Ubah DB_CONNECTION di .env Laravel agar konek ke container MySQL:

```env
DB_CONNECTION=
DB_HOST=
DB_PORT=
DB_DATABASE=
DB_USERNAME=
DB_PASSWORD=
```

## Config tambahan (optional)

Install dependency Laravel dari dalam container:

```bash
docker exec -it laravel_app composer install
```

Generate app key:
```bash
docker exec -it laravel_app php artisan key:generate
```