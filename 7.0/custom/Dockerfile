FROM 372768226/php:7.0-fpm-alpine-custom-ext-v1

MAINTAINER iiwoai  (https://github.com/docker-library/php/blob/b250a22ce67cd1232c11e0c17a0f1708ced6f4af/7.0/alpine3.7/fpm/Dockerfile)


## timezone
RUN apk update \
    && apk add --no-cache tzdata \
    && cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" > /etc/timezone \
    && apk del tzdata

RUN set -xe \
    && apk  update \
## deps
    && apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
        freetype-dev \
        libjpeg-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        libmemcached-dev \
        zlib-dev \
    && docker-php-ext-install -j$(nproc) iconv mcrypt \
    && docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-configure mysqli --with-mysqli=mysqlnd \
    && docker-php-ext-install mysqli \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
## 
## gettext
    && curl -fsSl 'http://ftp.gnu.org/pub/gnu/gettext/gettext-latest.tar.gz' -o gettext.tar.gz \
    && mkdir -p gettext \
    && tar -zxf gettext.tar.gz -C gettext --strip-components=1 \
    && rm gettext.tar.gz \
    && ( \
       cd gettext \
       && ./configure \
       && make -j$(nproc) \
       && make install \
    )\
    && rm -r gettext \
    && docker-php-ext-configure gettext --with-gettext \
    && docker-php-ext-install gettext \
    && docker-php-ext-enable gettext \
## igbinary
    && pecl install igbinary  && docker-php-ext-enable igbinary\
## ImageMagick
    && curl -fsSl 'https://www.imagemagick.org/download/ImageMagick.tar.gz' -o ImageMagick.tar.gz \
    && mkdir -p ImageMagick \
    && tar -zxf ImageMagick.tar.gz -C ImageMagick  --strip-components=1 \
    && rm ImageMagick.tar.gz \
    && ( \
       cd ImageMagick \
       && ./configure \ 
       && make -j$(nproc) \
       && make install \
    ) \
## imagick
    && curl -fsSl 'http://pecl.php.net/get/imagick-3.4.3.tgz' -o imagick.tgz \
    && mkdir -p /tmp/imagick \
    && tar -zxf imagick.tgz -C /tmp/imagick  --strip-components=1 \
    && rm imagick.tgz \
    && docker-php-ext-configure /tmp/imagick --with-imagick=/opt/local \
    && docker-php-ext-install /tmp/imagick \
    && rm -r ImageMagick /tmp/imagick \
    && docker-php-ext-enable imagick \
## msgpack
    && pecl install msgpack && docker-php-ext-enable msgpack\
## redis
    &&  pecl install redis-4.0.2 && docker-php-ext-enable redis \
## memcached
    && pecl install memcached-3.0.4  && docker-php-ext-enable memcached \
## swoole
    && pecl install swoole-2.2.0  && docker-php-ext-enable swoole \
## clean pkg
    \
    && runDeps="$( \
        scanelf --needed --nobanner --format '%n#p' --recursive /usr/local \
            | tr ',' '\n' \
            | sort -u \
            | awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }' \
    )" \
    && apk add --no-cache --virtual .php-rundeps $runDeps \
    \
    && apk del .build-deps \
    \
## clean pear, clean memcahe
    \
    && rm -rf /tmp/pear ~/.pearrc

RUN echo $'zend_extension=/usr/local/lib/php/extensions/no-debug-non-zts-20151012/opcache.so \n\
opcache.enable=1 \n\
opcache.enable_cli=1 \n\
opcache.memory_consumption=128 \n\
opcache.interned_strings_buffer=8\n\
opcache.max_accelerated_files=4000\n\
opcache.revalidate_freq=60\n\
opcache.enable_file_override=1\n\
opcache.fast_shutdown=1\n' > /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini  \
    && PHP_INI=/usr/local/etc/php/php.ini \
    && echo $'[PHP]\n\
post_max_size = 10M\n\
upload_max_filesize = 50M\n\
date.timezone = PRC\n\
cgi.fix_pathinfo = 0\n\
max_execution_time = 300\n' > $PHP_INI

