FROM php:8.2-apache

# 1. Instalar dependencias del sistema.
RUN apt-get update && apt-get install -y \
    git \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip \
    libpq-dev \
    && docker-php-ext-install pdo_pgsql mbstring exif pcntl bcmath gd

# 2. Instalar Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# 3. Configurar el directorio de trabajo
WORKDIR /var/www/html

# 4. Copiar los archivos del proyecto
COPY . .

# 5. Instalar dependencias de Laravel
RUN composer install --no-dev --optimize-autoloader

# 6. Configurar Apache
ENV APACHE_DOCUMENT_ROOT=/var/www/html/public
RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

# 7. Habilitar mod_rewrite
RUN a2enmod rewrite

# 8. Permisos (CORREGIDO PARA EVITAR PERMISSION DENIED)
# Se asegura la propiedad y los permisos de escritura antes de arrancar
RUN chown -R www-data:www-data /var/www/html/storage /var/www/html/bootstrap/cache && \
    chmod -R 775 /var/www/html/storage /var/www/html/bootstrap/cache

# Cambiar puerto de 80 a 8080
RUN sed -i 's/80/8080/g' /etc/apache2/ports.conf /etc/apache2/sites-available/*.conf

# 9. Puerto
EXPOSE 8080

# 10. Comando de inicio
CMD php artisan migrate --force && apache2-foreground
