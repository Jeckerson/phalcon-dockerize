FROM ubuntu:18.04

ARG PHALCON_VERSION

RUN apt update && apt -y upgrade && DEBIAN_FRONTEND=noninteractive apt install -y git \
        nano \
        wget \
        curl \
        unzip \
        nginx \
        supervisor \
        memcached \
        sqlite3 \
        mysql-client \
        postgresql-client \
        php7.2-common \
        php7.2-cli \
        php7.2-mbstring \
        php7.2-xml \
        php7.2-fpm \
        php7.2-curl \
        php7.2-mysql \
        php7.2-pgsql \
        php7.2-sqlite \
        php7.2-memcached \
        php7.2-gd \
        php-redis \
        php-apcu \
        php7.2-zip && \
    mkdir -p /run/php

RUN apt install -y  \
        make \
        gcc \
        php7.2-dev && \
    cd /opt && \
    rm -rf cphalcon && \
    git clone --depth=1 --branch "${PHALCON_VERSION}" "git://github.com/phalcon/cphalcon.git" && \
    cd cphalcon/build && \
    ./install && \
    cd /opt && \
    rm -rf php-psr && \
    git clone https://github.com/jbboehr/php-psr.git && \
    cd php-psr && \
    phpize && \
    ./configure && \
    make && make install && \
    echo 'extension=psr.so\nextension=phalcon.so' > /etc/php/7.2/fpm/conf.d/50-phalcon.ini && \
    echo 'extension=psr.so\nextension=phalcon.so' > /etc/php/7.2/cli/conf.d/50-phalcon.ini && \
    apt purge -y make \
        gcc \
        php7.2-dev && \
    apt clean

EXPOSE 9000 80 81 443

COPY ./etc /etc
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

CMD ["/usr/bin/supervisord"]
