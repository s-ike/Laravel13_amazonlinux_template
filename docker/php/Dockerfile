FROM amazonlinux:2023.12.20261021.0

COPY --from=composer:2.10 /usr/bin/composer /usr/bin/composer

# 初期設定
RUN \
    dnf update -y && \
    dnf install -y \
        zip \
        unzip \
        git \
        vim-enhanced \
        procps-ng \
        nginx && \
    dnf clean all

# PHPビルドに必要なパッケージ（php-buildのデフォルトconfigureオプションに対応）
RUN \
    dnf install -y --allowerasing \
        gcc \
        gcc-c++ \
        make \
        autoconf \
        bison \
        re2c \
        pkgconf-pkg-config \
        tar \
        bzip2 \
        xz \
        patch \
        diffutils \
        findutils \
        libxml2-devel \
        openssl-devel \
        sqlite-devel \
        libcurl-devel \
        oniguruma-devel \
        libicu-devel \
        bzip2-devel \
        readline-devel \
        libxslt-devel \
        libtidy-devel \
        libzip-devel \
        zlib-devel \
        libpng-devel \
        libjpeg-turbo-devel && \
    dnf clean all

# phpenv + php-build
ENV PHPENV_ROOT=/opt/phpenv
ENV PATH=${PHPENV_ROOT}/shims:${PHPENV_ROOT}/bin:${PATH}

ARG PHP_VERSION=8.5.12
RUN \
    git clone --depth 1 https://github.com/phpenv/phpenv.git ${PHPENV_ROOT} && \
    git clone --depth 1 https://github.com/php-build/php-build.git ${PHPENV_ROOT}/plugins/php-build && \
    phpenv install ${PHP_VERSION} && \
    phpenv global ${PHP_VERSION} && \
    phpenv rehash && \
    rm -rf /var/tmp/php-build

# docker/php/php.ini を追加で読み込む（先頭の":"はphp-build標準のconf.dも読む指定）
ENV PHP_INI_SCAN_DIR=:/usr/local/etc/php/conf.d

## 起動設定
RUN mkdir -p /var/run/php-fpm/ /var/log/php-fpm/ /usr/local/etc/php/conf.d/

# Set entrypoint
ENTRYPOINT php-fpm -y /usr/local/etc/php-fpm.conf && /usr/sbin/nginx -g "daemon off;"
