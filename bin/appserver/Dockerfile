FROM php:7.3-apache

# Get repository and install wget and vim
RUN apt-get update && apt-get install --no-install-recommends -y \
        wget \
        gnupg \
        git \
        unzip \
	    vim \
        htop

# Install PHP extensions deps
RUN apt-get update \
    && apt-get install --no-install-recommends -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        zlib1g-dev \
        libicu-dev \
        g++ \
        unixodbc-dev \
        libxml2-dev \
        libaio-dev \
        libmemcached-dev \
        freetds-dev \
        libssl-dev \
        openssl \
	locales \
	libgss3 \
	odbcinst \
	devscripts \
	debhelper \
	dh-exec \
	dh-autoreconf \
	libreadline-dev \
	libltdl-dev \
	libcurl4 \
	&& echo "en_US.UTF-8 UTF-8" > /etc/locale.gen
	
RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
RUN curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-release.list

RUN apt-get update && ACCEPT_EULA=Y apt-get install -y msodbcsql17 mssql-tools
RUN echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
RUN /bin/bash -c "source ~/.bashrc"

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- \
        --install-dir=/usr/local/bin \
        --filename=composer

# Install PHP extensions
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-configure pdo_dblib --with-libdir=/lib/x86_64-linux-gnu \
    && pecl install sqlsrv-5.6.1 \
    && pecl install pdo_sqlsrv-5.6.1 \
    && pecl install redis \
    && pecl install memcached \
    && pecl install xdebug \
    && docker-php-ext-install \
            iconv \
            mbstring \
            intl \
            gd \
            mysqli \
            pdo_mysql \
            pdo_dblib \
            soap \
            sockets \
            pcntl \
            ftp \
    && docker-php-ext-enable \
            sqlsrv \
            pdo_sqlsrv \
            redis \
            memcached

# Install APCu and APC backward compatibility
RUN pecl install apcu \
    && pecl install apcu_bc-1.0.3 \
    && docker-php-ext-enable apcu --ini-name 10-docker-php-ext-apcu.ini \
    && docker-php-ext-enable apc --ini-name 20-docker-php-ext-apc.ini

# Clean repository
RUN apt-get clean && rm -rf /var/lib/apt/lists/*
# Setup system timezone
RUN echo "America/Sao_Paulo" > /etc/timezone && dpkg-reconfigure -f noninteractive tzdata

#RUN service apache2 restart

RUN a2enmod rewrite && a2enmod headers && apache2ctl restart

RUN chown -R www-data:www-data /var/www/html

