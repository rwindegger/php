FROM php:fpm-alpine

MAINTAINER rene@windegger.wtf

ENV PHPREDIS_VERSION="3.1.4"

ENV PHPIZE_DEPS \
		autoconf \
		dpkg-dev dpkg \
		file \
		g++ \
		gcc \
		libc-dev \
		make \
		pkgconf \
		re2c

RUN apk add --update --no-cache \
		$PHPIZE_DEPS \
		curl \
		gnupg \
		mariadb-client-libs \
		geoip-dev \
		libintl \
		icu \
		icu-dev \
		libxml2-dev \
		freetype-dev \
		libjpeg-turbo-dev \
		libpng-dev \
		gmp \
		gmp-dev \
		libmemcached-dev \
		zlib-dev \
		cyrus-sasl-dev \
		git \
		autoconf \
		g++ \
		imagemagick-dev \
		pcre-dev \
		libtool \
		make \
		libssh2 \
		libssh2-dev \
		openldap-dev \
	&& docker-php-source extract \
    && curl -L -o /tmp/redis.tar.gz "https://github.com/phpredis/phpredis/archive/${PHPREDIS_VERSION}.tar.gz" \
    && tar xfz /tmp/redis.tar.gz \
    && rm -r /tmp/redis.tar.gz \
    && mv phpredis-$PHPREDIS_VERSION /usr/src/php/ext/redis \
    && git clone --branch php7 https://github.com/php-memcached-dev/php-memcached.git /usr/src/php/ext/memcached/ \
	&& rm -rf /usr/src/php/ext/memcached/.git \
	&& pecl install ssh2-1 APCu geoip-1.1.1 imagick \
	&& docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
	&& docker-php-ext-configure ldap --with-libdir=lib \
	&& docker-php-ext-configure memcached \
	&& docker-php-ext-enable ssh2 imagick apcu \
	&& docker-php-ext-install -j"$(getconf _NPROCESSORS_ONLN)" \
		intl \
		zip \
		soap \
		iconv \
		gmp \
		gd \
		mbstring \
		mysqli \
		pdo_mysql \
		zip \
		ldap \
		opcache \
		memcached \
		redis \
	&& docker-php-source delete \
	&& mkdir -p /usr/local/var/geoip \
	&& curl -L -o /usr/local/var/geoip/GeoIPCity.dat.gz http://geolite.maxmind.com/download/geoip/database/GeoLiteCity.dat.gz \
	&& gunzip /usr/local/var/geoip/GeoIPCity.dat.gz \
	&& apk del --no-cache \
		autoconf \
		g++ \
		libtool \
		make \
		$PHPIZE_DEPS \
		pcre-dev \
		zlib-dev \
		cyrus-sasl-dev \
		git \
	&& rm -rf /var/cache/apk/* \ 
	&& rm -rf /tmp/*

RUN echo $'always_populate_raw_post_data=-1\n\
extension=geoip.so\n\
geoip.custom_directory=/usr/local/var/geoip\n' > /usr/local/etc/php/conf.d/geoip.ini
	
RUN echo $'opcache.memory_consumption=128\n\
opcache.interned_strings_buffer=8\n\
opcache.max_accelerated_files=4000\n\
opcache.revalidate_freq=60\n\
opcache.fast_shutdown=1\n\
opcache.enable_cli=1\n' > /usr/local/etc/php/conf.d/opcache-recommended.ini
	
RUN echo $'apc.shm_segments=1\n\
apc.shm_size=256M\n\
apc.num_files_hint=7000\n\
apc.user_entries_hint=4096\n\
apc.ttl=7200\n\
apc.user_ttl=7200\n\
apc.gc_ttl=3600\n\
apc.max_file_size=1M\n\
apc.stat=1\n'> /usr/local/etc/php/conf.d/apcu-recommended.ini
	
RUN echo $'max_input_time=60\n\
max_execution_time=120\n\
upload_max_filesize=64M\n\
post_max_size=64M\n\
memory_limit=256M\n\
expose_php=0\n' > /usr/local/etc/php/conf.d/disableexpose.ini
	
RUN sed -i -e 's/listen.*/listen = 0.0.0.0:9000/' /usr/local/etc/php-fpm.conf

CMD ["php-fpm"]