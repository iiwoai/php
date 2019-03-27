FROM harbor.qudian.com/base/fpm-stretch-custom-ext-v1:latest

MAINTAINER iiwoai  

# prevent Debian's PHP packages from being installed
# https://github.com/docker-library/php/pull/542
RUN set -eux; \
	{ \
		echo 'Package: php*'; \
		echo 'Pin: release *'; \
		echo 'Pin-Priority: -1'; \
	} > /etc/apt/preferences.d/no-debian-php

# dependencies required for running "phpize"
# (see persistent deps below)

RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" > /etc/timezone 



# persistent / runtime deps
RUN apt-get update && apt-get install -y \
		$PHPIZE_DEPS \
		ca-certificates \
		libfreetype6-dev \
        libmcrypt-dev \
        libpng-dev \
        libjpeg-dev \
        libmemcached-dev \
        libzip-dev \ 
	--no-install-recommends && rm -r /var/lib/apt/lists/* \
	&& docker-php-ext-install -j$(nproc) iconv mcrypt \
    && docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-configure mysqli --with-mysqli=mysqlnd \
    && docker-php-ext-install mysqli \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install zip 