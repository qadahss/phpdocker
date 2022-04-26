FROM php:7.2-fpm

# Set working directory
WORKDIR /var/www
RUN mkdir -p /usr/share/man/man1

# Install dependencies
RUN apt-get update && apt-get install -y \
    cron \
    default-jre \
    iputils-ping \
    build-essential \
    libpng-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    locales \
    zip \
    jpegoptim optipng pngquant gifsicle \
    vim \
    unzip \
    git \
    curl \
    libmemcached11 \
    libmemcachedutil2 \
    libxml2-dev \
    libmemcached-dev 

# Install pdo_pgsql extension

RUN apt-get update && apt-get install -y libpq-dev

RUN docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql

RUN docker-php-ext-install pdo pdo_pgsql

# Install Memcached
RUN pecl install memcached \ && docker-php-ext-enable memcached

# Install Redis если больше redis >= 5 версии то надо обновлять redisbundle т.к redis->getMultiple #Deprecated
RUN pecl install -o -f redis-4.3.0 \ &&  rm -rf /tmp/pear \ &&  docker-php-ext-enable redis

# Install xdebug
RUN pecl install -o -f xdebug-2.9.8 \ &&  rm -rf /tmp/pear \ &&  docker-php-ext-enable xdebug

# Install extensions
RUN docker-php-ext-install pdo_mysql mbstring zip exif pcntl soap sockets bcmath

# Install gearman
RUN apt-get -y --allow-unauthenticated install libgearman-dev wget unzip \
    && cd /tmp \
    && wget https://github.com/wcgallego/pecl-gearman/archive/gearman-2.0.3.zip \
    && unzip gearman-2.0.3.zip \
    && mv pecl-gearman-gearman-2.0.3 pecl-gearman \
    && cd pecl-gearman \
    && phpize \
    && ./configure \
    && make -j$(nproc) \
    && make install \
    && cd / \
    && rm /tmp/gearman-2.0.3.zip \
    && rm -r /tmp/pecl-gearman \
    && docker-php-ext-enable gearman

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# install mysql client
RUN apt-get update && apt-get -y install mariadb-client

COPY phing-latest.phar /phing.phar


# init crontab form www-data
RUN mkdir /var/log/cron
RUN touch /var/log/cron/cron.log
RUN touch /mycrontab
RUN chown -R www-data:www-data /mycrontab
RUN /usr/bin/crontab -u www-data /mycrontab

# start php-fpm
CMD ["php-fpm"]
