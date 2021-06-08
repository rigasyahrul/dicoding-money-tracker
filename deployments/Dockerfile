FROM php:7.2-apache

# Install Extension
RUN apt-get update \
  && apt-get install -y git unzip libpng-dev libwebp-dev libgmp-dev libjpeg62-turbo-dev libfreetype6-dev zlib1g-dev libzip-dev libpq-dev libicu-dev \
  && docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp \
  && docker-php-ext-install zip gd pdo_mysql mysqli \
  && rm -rf /var/lib/apt/lists/* \
  && a2enmod rewrite

# Get composer-setup file
RUN cd /tmp && \
  php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"

# Install composer
RUN php /tmp/composer-setup.php --install-dir=/usr/local/bin --filename=composer

# Set workdir
WORKDIR /var/app

# Copy virtual host file
COPY 000-default.conf /etc/apache2/sites-available/

# Copy php.ini
COPY php.ini $PHP_INI_DIR/php.ini

# Enable mod_rewrite
RUN if command -v a2enmod >/dev/null 2>&1; then \
      a2enmod rewrite headers \
    ;fi

RUN service apache2 restart
