FROM php:8.3-apache-bookworm

# Supprime les plaintes de debconf pour l'installation interactive des paquets apt
ARG DEBIAN_FRONTEND=noninteractive

# Mise à jour et installation des dépendances essentielles
RUN apt-get -y update --fix-missing && \
    apt-get upgrade -y && \
    apt-get --no-install-recommends install -y apt-utils && \
    rm -rf /var/lib/apt/lists/*

# Installation des outils utiles et des bibliothèques importantes
RUN apt-get -y update && \
    apt-get -y --no-install-recommends install nano wget dialog \
    libsqlite3-dev libsqlite3-0 default-mysql-client zlib1g-dev \
    libzip-dev libicu-dev apt-utils build-essential git curl \
    libonig-dev iputils-ping libcurl4 libcurl4-openssl-dev zip openssl \
    libssl-dev && \
    rm -rf /var/lib/apt/lists/*

# Installation de Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Installation de Symfony CLI
RUN curl -sS https://get.symfony.com/cli/installer | bash -s -- --install-dir=/usr/local/bin && \
    chmod +x /usr/local/bin/symfony

# Installation de Xdebug
RUN pecl install xdebug-3.3.1 && \
    docker-php-ext-enable xdebug && \
    mkdir /var/log/xdebug

# Installation de Redis
RUN pecl install redis-6.0.2 && \
    docker-php-ext-enable redis

# Installation de l'extension MongoDB
RUN pecl install mongodb && \
    docker-php-ext-enable mongodb

# Installation de Imagick
RUN apt-get update && \
    apt-get -y --no-install-recommends install --fix-missing libmagickwand-dev && \
    rm -rf /var/lib/apt/lists/*

# Commit spécifique pour Imagick
ARG IMAGICK_COMMIT="28f27044e435a2b203e32675e942eb8de620ee58"

RUN cd /usr/local/src && \
    git clone https://github.com/Imagick/imagick && \
    cd imagick && \
    git checkout ${IMAGICK_COMMIT} && \
    phpize && \
    ./configure && \
    make && \
    make install && \
    cd .. && \
    rm -rf imagick && \
    docker-php-ext-enable imagick

# Installation des extensions PHP
RUN docker-php-ext-install pdo_mysql && \
    docker-php-ext-install pdo_sqlite && \
    docker-php-ext-install bcmath && \
    docker-php-ext-install mysqli && \
    docker-php-ext-install curl && \
    docker-php-ext-install zip && \
    docker-php-ext-install -j$(nproc) intl && \
    docker-php-ext-install mbstring && \
    docker-php-ext-install gettext && \
    docker-php-ext-install calendar && \
    docker-php-ext-install exif

# Installation de Freetype
RUN apt-get -y update && \
    apt-get --no-install-recommends install -y libfreetype6-dev libjpeg62-turbo-dev libpng-dev && \
    rm -rf /var/lib/apt/lists/* && \
    docker-php-ext-configure gd --enable-gd --with-freetype --with-jpeg && \
    docker-php-ext-install gd

# Assurez-vous qu'un répertoire SSL existe
RUN mkdir -p /etc/apache2/ssl

# Activation de la prise en charge SSL
RUN a2enmod ssl && a2enmod rewrite

# Activation des modules Apache nécessaires
RUN a2enmod rewrite headers

# Nettoyage
RUN rm -rf /usr/src/*

# Définir le répertoire de travail
WORKDIR /var/www/html

# Permissions pour Symfony
RUN chown -R www-data:www-data /var/www/html && chmod -R 775 /var/www/html

# Exposer le port 80
EXPOSE 80
