FROM alpine:latest

MAINTAINER Daniel Wiesendorf <daniel.wiesendorf@googlemail.com>

# Set configuration defaults
ENV TIMEZONE Europe/Berlin
ENV PHP_MEMORY_LIMIT 512M
ENV MAX_UPLOAD 50M
ENV PHP_MAX_FILE_UPLOAD 200
ENV PHP_MAX_POST 100M

# Install dependencies
RUN apk --no-cache --repository http://dl-cdn.alpinelinux.org/alpine/edge/testing  add \
        tzdata \
        ca-certificates \
        git \
        curl \
        unzip \
        php7 \
        php7-xml \
        php7-zip \
        php7-xmlreader \
        php7-zlib \
        php7-opcache \
        php7-mcrypt \
        php7-openssl \
        php7-curl \
        php7-json \
        php7-dom \
        php7-phar \
        php7-mbstring \
        php7-cgi \
        php7-pcntl && \
    ln -s /usr/bin/php7 /usr/bin/php

# Configure system timezone
RUN cp /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && \
  	echo "${TIMEZONE}" > /etc/timezone

# Configure php form env vars
RUN sed -i "s|;date.timezone =.*|date.timezone = ${TIMEZONE}|" /etc/php7/php.ini && \
    sed -i "s|memory_limit =.*|memory_limit = ${PHP_MEMORY_LIMIT}|" /etc/php7/php.ini && \
  	sed -i "s|upload_max_filesize =.*|upload_max_filesize = ${MAX_UPLOAD}|" /etc/php7/php.ini && \
  	sed -i "s|max_file_uploads =.*|max_file_uploads = ${PHP_MAX_FILE_UPLOAD}|" /etc/php7/php.ini && \
  	sed -i "s|post_max_size =.*|max_file_uploads = ${PHP_MAX_POST}|" /etc/php7/php.ini && \
  	sed -i "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/" /etc/php7/php.ini

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename=composer

# Install php-pm
RUN curl -L -o /tmp/php-pm.tar.gz https://github.com/php-pm/php-pm/archive/master.tar.gz && \
    tar -xzf /tmp/php-pm.tar.gz -C / && \
    composer install -d /php-pm-master && \
    ln -s /php-pm-master/bin/ppm /usr/bin/ppm

# Cleanup
RUN apk del \
        tzdata && \
  	rm -rf \
        /var/cache/apk/* \
        /tmp/*

# Create workdir
RUN mkdir /www

# Set Workdir
WORKDIR /www

# Expose volumes
VOLUME ["/www"]

# Expose ports
EXPOSE 9000

# Entry point
CMD ["/usr/bin/ppm", "start"]
