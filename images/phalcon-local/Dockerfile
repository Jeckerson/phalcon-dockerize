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

# Download and Install composer
RUN wget https://github.com/composer/composer/releases/download/1.10.5/composer.phar && \
    wget https://github.com/phalcon/zephir/releases/download/0.12.18/zephir.phar && \
    chmod ugo+x composer.phar zephir.phar && \
    mv composer.phar /usr/local/bin/composer && \
    mv zephir.phar /usr/local/bin/zephir && \
    composer global require hirak/prestissimo

RUN apt install -y  \
        make \
        gcc \
        re2c \
        autoconf \
        automake \
        build-essential \
        php7.2-dev

# Download and install Zephir
RUN cd /opt && \
    rm -rf php-zephir-parser && \
    git clone git://github.com/phalcon/php-zephir-parser.git && \
    cd php-zephir-parser && \
    phpize && \
    ./configure && \
    make && make install && \
    echo 'extension=zephir_parser.so' > /etc/php/7.2/cli/conf.d/48-zephir_parser.ini

# Download, install and enable PSR PHP module
RUN cd /opt && \
    rm -rf php-psr && \
    git clone https://github.com/jbboehr/php-psr.git && \
    cd php-psr && \
    phpize && \
    ./configure && \
    make && make install && \
    echo 'extension=psr.so' > /etc/php/7.2/fpm/conf.d/49-psr.ini && \
    echo 'extension=psr.so' > /etc/php/7.2/cli/conf.d/49-psr.ini

# Git clone, build, compile, install and enable Phalcon PHP Module
RUN cd /opt && \
    rm -rf cphalcon && \
    git clone --depth=1 --branch "${PHALCON_VERSION}" "https://github.com/phalcon/cphalcon.git" && \
    cd cphalcon && \
    zephir fullclean && \
    zephir build && \
    cd ext/ && \
    ./install && \
    echo 'extension=phalcon.so' > /etc/php/7.2/fpm/conf.d/50-phalcon.ini && \
    echo 'extension=phalcon.so' > /etc/php/7.2/cli/conf.d/50-phalcon.ini && \
    apt purge -y make \
        gcc \
        re2c \
        autoconf \
        automake \
        build-essential \
        php7.2-dev && \
    apt clean

RUN php -r 'echo Phalcon\Version::get() . PHP_EOL;'

COPY ./etc /etc
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

EXPOSE 9000 80 81 8080 443

CMD ["/usr/bin/supervisord"]
