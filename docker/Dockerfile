FROM php:7.1-fpm-alpine

ENV VARNISH_VERSION=5.0.0
ENV VARNISH_MODULES_VERSION=0.11.0
ENV NGINX_VERSION=1.9.15
ENV NGINX_CACHE_PURGE_VERSION=2.3

ADD http://git.alpinelinux.org/cgit/aports/plain/main/varnish/musl-mode_t.patch /
COPY install-nginx.sh /

# See https://www.varnish-cache.org/docs/trunk/installation/install.html
RUN apk add --no-cache pcre \
    && apk add --no-cache --virtual .build-deps autoconf automake bash libtool libedit-dev linux-headers make ncurses-dev openssl pcre-dev python py-docutils \
    && apk add --no-cache g++ git openssl \
    && wget https://repo.varnish-cache.org/source/varnish-$VARNISH_VERSION.tar.gz \
    && tar xf varnish-$VARNISH_VERSION.tar.gz \
    && cd varnish-$VARNISH_VERSION \
    && ./autogen.sh \
    && ./configure --with-rst2man=/bin/true \
    && patch -p1 < /musl-mode_t.patch \
    && make \
    && make install \
    && cd .. \
    && rm -r varnish-$VARNISH_VERSION.tar.gz varnish-$VARNISH_VERSION /musl-mode_t.patch \

    # Varnish modules
    && wget -O varnish-modules.tar.gz https://github.com/varnish/varnish-modules/archive/${VARNISH_MODULES_VERSION}.tar.gz \
    && tar xf varnish-modules.tar.gz \
    && cd varnish-modules-${VARNISH_MODULES_VERSION} \
    && PKG_CONFIG_PATH=/usr/local/lib/pkgconfig ./bootstrap \
    && PKG_CONFIG_PATH=/usr/local/lib/pkgconfig ./configure \
    && make \
    && make install \
    && cd .. \
    && rm -r varnish-modules-${VARNISH_MODULES_VERSION} varnish-modules.tar.gz \

    # Nginx
    && ./install-nginx.sh \
    && apk del .build-deps

RUN wget -O /usr/local/bin/composer https://getcomposer.org/download/1.3.2/composer.phar \
    && chmod +x /usr/local/bin/composer

WORKDIR /app
