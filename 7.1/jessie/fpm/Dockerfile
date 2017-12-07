FROM php:7.1-fpm

ADD mocking_ca.crt /tmp/
ADD memory.ini /tmp/
ADD install-composer.sh /tmp
RUN chmod +x /tmp/install-composer.sh \
    && apt-get update \
    && apt-get install apt-transport-https ca-certificates \
    && curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - \
    && curl https://packages.microsoft.com/config/debian/8/prod.list > /etc/apt/sources.list.d/mssql-release.list \
    && apt-get update \
    && ACCEPT_EULA=Y apt-get -y install zlib1g-dev libmemcached-dev zip unzip git vim libssl-dev netcat-openbsd ant wget unixodbc-dev  msodbcsql locales-all freetds-bin tdsodbc \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install zip \
    && docker-php-ext-install json \
    && pecl install xdebug \
    && pecl install -o -f redis \
    && docker-php-ext-enable xdebug \
    && docker-php-ext-enable redis \
    && pecl install pdo_sqlsrv \
    && docker-php-ext-enable pdo_sqlsrv \
    && /tmp/install-composer.sh \
    && rm -rf /tmp/pear \
    && useradd bgflashagent \
    && mkdir -p /usr/local/share/ca-certificates \
    && cp /tmp/mocking_ca.crt /usr/local/share/ca-certificates/ \
    && cp -v /tmp/memory.ini /usr/local/etc/php/conf.d \
    && chmod 777 -v /usr/local/share/ca-certificates \
    && chmod 644 -v /usr/local/share/ca-certificates/mocking_ca.crt \
    && update-ca-certificates

#install all the system dependencies and enable PHP modules
RUN apt-get update && apt-get install -y \
          libicu-dev \
          libpq-dev \
          libmcrypt-dev \
        && rm -r /var/lib/apt/lists/* \
        && docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd \
        && docker-php-ext-install \
          intl \
          mbstring \
          mcrypt \
          pcntl \
          pdo_mysql \
          pdo_pgsql \
          pgsql \
          zip \
          opcache


#install composer and other tools
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin/ --filename=composer

RUN curl -sL https://deb.nodesource.com/setup_8.x | bash -
RUN apt-get install -y build-essential
RUN apt-get install -y nodejs
RUN /usr/bin/npm install -g gulp
RUN /usr/bin/npm install -g bower

ADD freetds.conf /etc/freetds/freetds.conf
