FROM php:7.4-apache-buster

# Indico la versión que voy a usar de Drupal:
# - https://www.drupal.org/node/3060/release
ENV DRUPAL_VERSION 8.9.7

# Extensiones que necesito de PHP para Drupal.
RUN set -eux; \
  \
  if command -v a2enmod; then \
    a2enmod rewrite; \
  fi; \
  \
  savedAptMark="$(apt-mark showmanual)"; \
  \
  apt-get update; \
  apt-get install -y --no-install-recommends \
    libfreetype6-dev \
    libjpeg-dev \
    libpng-dev \
    libpq-dev \
    libzip-dev \
  ; \
  \
  docker-php-ext-configure gd \
    --with-freetype \
    --with-jpeg=/usr \
  ; \
  \
  docker-php-ext-install -j "$(nproc)" \
    gd \
    opcache \
    pdo_mysql \
    pdo_pgsql \
    zip \
  ; \
  \
  # reset apt-mark's "manual" list so that "purge --auto-remove" will remove all build dependencies
  apt-mark auto '.*' > /dev/null; \
  apt-mark manual $savedAptMark; \
  ldd "$(php -r 'echo ini_get("extension_dir");')"/*.so \
    | awk '/=>/ { print $3 }' \
    | sort -u \
    | xargs -r dpkg-query -S \
    | cut -d: -f1 \
    | sort -u \
    | xargs -rt apt-mark manual; \
  \
  apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false; \
  rm -rf /var/lib/apt/lists/*

# Valores por defecto para OPCACHE:
# - https://secure.php.net/manual/en/opcache.installation.php
RUN { \
    echo 'opcache.memory_consumption=128'; \
    echo 'opcache.interned_strings_buffer=8'; \
    echo 'opcache.max_accelerated_files=4000'; \
    echo 'opcache.revalidate_freq=60'; \
    echo 'opcache.fast_shutdown=1'; \
  } > /usr/local/etc/php/conf.d/opcache-recommended.ini

# Otros valores para aplicar a PHP.
RUN echo "upload_max_filesize = 128M" >> /usr/local/etc/php/conf.d/0-upload_large_dumps.ini \
  && echo "post_max_size = 128M" >> /usr/local/etc/php/conf.d/0-upload_large_dumps.ini \
  && echo "memory_limit = 2G" >> /usr/local/etc/php/conf.d/0-upload_large_dumps.ini \
  && echo "max_execution_time = 600" >> /usr/local/etc/php/conf.d/0-upload_large_dumps.ini \
  && echo "max_input_vars = 5000" >> /usr/local/etc/php/conf.d/0-upload_large_dumps.ini

# Activo XDebug:
# - https://dev.to/fuenrob/configurar-docker-con-xdebug-y-vs-code-252h
RUN pecl install -f xdebug \
  && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini;

# Instalo Composer:
# - https://github.com/drupal/drupal/blob/9.0.1/composer.lock#L4052-L4053
COPY --from=composer:1.10 /usr/bin/composer /usr/local/bin/
ENV COMPOSER_MEMORY_LIMIT -1

# Instalo Node (via package manager para Debian):
# - https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions
RUN curl -sL https://deb.nodesource.com/setup_14.x | bash -
RUN apt-get update \
  && apt-get install -y \
  nodejs

# Instalo Drupal.
WORKDIR /opt/drupal
RUN set -eux; \
  export COMPOSER_HOME="$(mktemp -d)"; \
  composer create-project --no-interaction "drupal/recommended-project:$DRUPAL_VERSION" ./; \
  # Creo las carpetas "custom" en modules y themes.
  mkdir -p /opt/drupal/web/modules/custom; \
  mkdir -p /opt/drupal/web/themes/custom; \
  mkdir -p /opt/drupal/web/libraries; \
  mkdir -p /opt/drupal/private-files; \
  chown -R www-data:www-data /opt/drupal/web/sites /opt/drupal/web/modules /opt/drupal/web/themes /opt/drupal/web/libraries /opt/drupal/private-files; \
  rmdir /var/www/html; \
  ln -sf /opt/drupal/web /var/www/html; \
  # Borro la caché de composer.
  rm -rf "$COMPOSER_HOME";

ENV PATH=${PATH}:/opt/drupal/vendor/bin

# Instalo git y vim.
RUN apt-get update \
  && apt-get install -y \
  git \
  vim

# Instalo Drush.
RUN apt-get update && apt-get install -y \
  wget; \
  wget -O drush.phar https://github.com/drush-ops/drush-launcher/releases/download/0.6.0/drush.phar && \
  chmod +x drush.phar && \
  mv drush.phar /usr/local/bin/drush

# Instalo los módulos que uso por defecto.
RUN composer require \
  # Módulos.
  'drupal/admin_toolbar:^2.4' \
  'drupal/adminimal_admin_toolbar:^1.11' \
  'drupal/allowed_formats:^1.3' \
  'drupal/amswap:^3.0' \
  'drupal/better_exposed_filters:^5.0' \
  'drupal/devel:^4.0' \
  'drupal/devel_php:^1.3' \
  'drupal/entity_usage:^2.0@beta' \
  'drupal/eu_cookie_compliance:^1.13' \
  'drupal/field_group:^3.1' \
  'drupal/libraries:^3.0@alpha' \
  'drupal/metatag:^1.14' \
  'drupal/paragraphs:^1.12' \
  'drupal/pathauto:^1.8' \
  'drupal/redirect:^1.6' \
  'drupal/simple_sitemap:^3.7' \
  'drupal/smtp:^1.0' \
  'drupal/twig_tweak:^2.8' \
  'drupal/verf:^1.0' \
  'drupal/views_bulk_operations:^3.9' \
  'drupal/webform:^6.0@alpha' \
  'drupal/webform_views:^5.0@alpha' \
  # Themes.
  'drupal/adminimal_theme:^1.6' \
  # Otros.
  'drush/drush:^10.3' \
  ;\
  # Cambio los permisos de los módulos añadidos.
  chown -R www-data:www-data /opt/drupal/web/modules /opt/drupal/web/themes /opt/drupal/web/libraries

# Copio archivos settings.php, settings.local.php y default.services.yml
COPY config/settings.php /opt/drupal/web/sites/default/settings.php
COPY config/settings.local.php /opt/drupal/web/sites/default/settings.local.php
COPY config/default.services.yml /opt/drupal/web/sites/default.services.yml

# Ubicación para el archivo de configuración y asigno permisos.
RUN mkdir -p /opt/drupal/web/sites/default/files/config/sync; \
  chown -R www-data:www-data /opt/drupal/web/sites
