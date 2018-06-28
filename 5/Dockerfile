# From official php-fpm
FROM php:5-fpm
LABEL maintainer="Kawin Viriyaprasopsook <bouroo@gmail.com>"

ARG	timezone=Asia/Bangkok
ENV	TERM xterm
ENV	DEBIAN_FRONTEND noninteractive

ENV	LANG en_US.UTF-8
ENV	LC_ALL en_US.UTF-8
ENV	TZ $timezone

# Update OS
RUN	apt-get update && apt-get -y dist-upgrade

# Change locale
RUN apt update && apt -y install locales tzdata \
	&& locale-gen en_US.UTF-8 && locale-gen th_TH.UTF-8 \
	&& update-locale en_US.UTF-8 \
	&& echo $timezone > /etc/timezone \
	&& cp /usr/share/zoneinfo/$timezone /etc/localtime \
	&& dpkg-reconfigure tzdata

# Copy php.ini
# modify from https://github.com/php/php-src/blob/master/php.ini-production
COPY config/php.ini /usr/local/etc/php/
RUN chmod 0644 /usr/local/etc/php/php.ini

# Add deps
RUN apt-get update && apt-get install -y \
	default-libmysqlclient-dev \
	git-core \
	libbz2-dev \
	libfreetype6-dev \
	libgmp-dev \
	libjpeg-dev \
	libmcrypt-dev \
	libmemcached-dev \
	libpng-dev \
	libpq-dev \
	libssl-dev \
	libxml2-dev \
	wget \
	zlib1g-dev

# Add PHP ext
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
	&& docker-php-ext-install -j$(nproc) \
	bcmath \
	gd \
	#gmp \
	iconv \
	json \
	mysqli \
	pcntl \
	pdo_mysql \
	pdo_pgsql  \
	soap \
	sockets \
	zip
RUN pecl install memcached-2.2.0
RUN docker-php-ext-enable memcached

# Composer
RUN php -r "readfile('https://getcomposer.org/installer');" > composer-setup.php \
	&& php composer-setup.php \
	&& php -r "unlink('composer-setup.php');" \
	&& chmod +x composer.phar \
	&& mv composer.phar /usr/bin/composer

# Add global composer bin dir to PATH
ENV PATH /root/.composer/vendor/bin:$PATH