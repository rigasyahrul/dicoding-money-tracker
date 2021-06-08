FROM php:7.2-apache

ENV BUILD_PACKAGES="libpng-dev libwebp-dev libgmp-dev libjpeg62-turbo-dev libfreetype6-dev zlib1g-dev libzip-dev libpq-dev libicu-dev" \
  APP_PATH="/var/app"

# Install required app
RUN apt-get update \
  && apt-get install -y git unzip $BUILD_PACKAGES

# Install nodejs
RUN curl -sL https://deb.nodesource.com/setup_12.x | bash
RUN apt-get install -y nodejs

RUN docker-php-ext-configure \
    gd --with-freetype-dir=/usr/include/ --with-webp-dir=/usr/include/ --with-jpeg-dir=/usr/include/

ARG INSTALL_INTL=true

RUN if [ ${INSTALL_INTL} = true ]; then \
    # Install intl and requirements
    apt-get install -y zlib1g-dev libicu-dev g++ && \
    docker-php-ext-configure intl && \
    docker-php-ext-install intl \
;fi

# Install built-in extensions
RUN docker-php-ext-install \
    pdo_mysql \
    mbstring \
    opcache \
    mysqli \
    bcmath \
    iconv \
    exif \
    zip \
    gd \
    pdo

# Set-up environment settings for composer
ENV COMPOSER_ALLOW_SUPERUSER=1 \
  PATH=$APP_PATH:$APP_PATH/vendor/bin:/root/.composer/vendor/bin:$PATH \
  PHP_USER_ID=33 \
  TERM=linux

# Get composer-setup file
RUN cd /tmp \
  && php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
  && php /tmp/composer-setup.php --install-dir=/usr/local/bin --filename=composer

# Clean up
RUN apt-get remove --purge -y $BUILD_PACKAGES \
  && rm -rf /var/lib/apt/lists/*

# Set workdir
WORKDIR /var/app

# Copy virtual host file
COPY 000-default.conf /etc/apache2/sites-available/

# Copy PHP config
COPY php.ini $PHP_INI_DIR/php.ini

# Enable mod_rewrite
RUN if command -v a2enmod >/dev/null 2>&1; then \
      a2enmod rewrite headers \
    ;fi
