FROM omatech/base

#Actualizamos el sistema
RUN apt-get update && apt-get upgrade -y

#Instalación de Apache2
RUN /bin/echo "deb http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial main" >> /etc/apt/sources.list.d/apache.list \
 && /bin/echo "deb-src http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial main" >> /etc/apt/sources.list.d/apache.list \
 && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 14AA40EC0831756756D7F66C4F4EA0AAE5267A6C \
 && apt-get update \
 && apt-get install -y apache2 apache2-utils \
 && mkdir -p /var/lock/apache2 /var/run/apache2 \
 && sed -i "s|#AddDefaultCharset UTF-8|AddDefaultCharset UTF-8|g" /etc/apache2/conf-enabled/charset.conf \
 && /bin/echo -e "[program:apache2]\ncommand=/bin/bash -c 'source /etc/apache2/envvars && exec /usr/sbin/apache2 -D FOREGROUND'\n" >> /etc/supervisor/conf.d/supervisord.conf \
 && /bin/echo -e "stdout_logfile=/dev/stdout\n" >> /etc/supervisor/conf.d/supervisord.conf \
 && /bin/echo -e "stdout_logfile_maxbytes=0\n" >> /etc/supervisor/conf.d/supervisord.conf \
 && a2enmod rewrite expires ssl headers actions alias \
 && sed -i '/<Directory \/var\/www\/>/,/<\/Directory>/ s/Options Indexes FollowSymLinks/Options -Indexes +FollowSymLinks/' /etc/apache2/apache2.conf \
 && sed -i '/<Directory \/var\/www\/>/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/apache2/apache2.conf

EXPOSE 80
EXPOSE 443

#Instalación de PHP 5.6
RUN /bin/echo "deb http://ppa.launchpad.net/ondrej/php/ubuntu xenial main" >> /etc/apt/sources.list.d/php.list \
 && /bin/echo "deb-src http://ppa.launchpad.net/ondrej/php/ubuntu xenial main" >> /etc/apt/sources.list.d/php.list \
 && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 14AA40EC0831756756D7F66C4F4EA0AAE5267A6C \
 && apt-get update \
 && apt-get install -y php5.6 php5.6-mysql php5.6-dom php5.6-mcrypt php5.6-mbstring php5.6-zip php5.6-gd php5.6-memcached php5.6-curl php5.6-dev php5.6-intl php5.6-soap \
 && sed -i "s/short_open_tag = Off/short_open_tag = On/" /etc/php/5.6/apache2/php.ini \
 && sed -i "s/memory_limit = 128M/memory_limit = 512M/" /etc/php/5.6/apache2/php.ini \
 && sed -i "s/error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT/error_reporting = E_ALL & ~E_NOTICE & ~E_DEPRECATED & ~E_STRICT/" /etc/php/5.6/apache2/php.ini \
 && sed -i "s/display_errors = Off/display_errors=On/" /etc/php/5.6/apache2/php.ini \
 && sed -i "s/post_max_size = 8M/post_max_size = 50M/" /etc/php/5.6/apache2/php.ini \
 && sed -i "s/upload_max_filesize = 2M/upload_max_filesize = 130M/" /etc/php/5.6/apache2/php.ini \
 && sed -i "s/; max_input_vars = 1000/max_input_vars = 1000/" /etc/php/5.6/apache2/php.ini

#Xdebug
RUN apt-get install -y php5.6-xdebug \
 && phpenmod xdebug \
 && /bin/echo -e "xdebug.remote_host = 127.0.0.1" >> /etc/php/5.6/apache2/conf.d/20-xdebug.ini \
 && /bin/echo -e "xdebug.idekey = 'xdebug'" >> /etc/php/5.6/apache2/conf.d/20-xdebug.ini \
 && /bin/echo -e "xdebug.remote_enable = 1" >> /etc/php/5.6/apache2/conf.d/20-xdebug.ini \
 && /bin/echo -e "xdebug.remote_connect_back = 0" >> /etc/php/5.6/apache2/conf.d/20-xdebug.ini \
 && /bin/echo -e "xdebug.remote_port = 9000" >> /etc/php/5.6/apache2/conf.d/20-xdebug.ini \
 && /bin/echo -e "xdebug.max_nesting_level = 300" >> /etc/php/5.6/apache2/conf.d/20-xdebug.ini \
 && /bin/echo -e "xdebug.scream = 0" >> /etc/php/5.6/apache2/conf.d/20-xdebug.ini \
 && /bin/echo -e "xdebug.cli_color = 1" >> /etc/php/5.6/apache2/conf.d/20-xdebug.ini \
 && /bin/echo -e "xdebug.show_local_vars = 1" >> /etc/php/5.6/apache2/conf.d/20-xdebug.ini \
 && /bin/echo -e "xdebug.profiler_enable = 1" >> /etc/php/5.6/apache2/conf.d/20-xdebug.ini \
 && /bin/echo '#!/bin/bash' > /var/xdebugswitch.sh \
 && /bin/echo '# Simple script to enable or disable the xdebug extension for php' >> /var/xdebugswitch.sh \
 && /bin/echo 'case $1 in' >> /var/xdebugswitch.sh \
 && /bin/echo 'enabled)' >> /var/xdebugswitch.sh \
 && /bin/echo     'phpenmod xdebug' >> /var/xdebugswitch.sh \
 && /bin/echo     'sed -i "s/xdebug.idekey = "xdebug"/xdebug.idekey = $2/" /etc/php/5.6/apache2/conf.d/20-xdebug.ini' >> /var/xdebugswitch.sh \
 && /bin/echo     'sed -i "s/xdebug.remote_host = 127.0.0.1/xdebug.remote_host = $3/" /etc/php/5.6/apache2/conf.d/20-xdebug.ini' >> /var/xdebugswitch.sh \
 && /bin/echo     'service apache2 restart' \
 && /bin/echo     'echo "Xdebug is Enabled"' >> /var/xdebugswitch.sh \
 && /bin/echo ';;' >> /var/xdebugswitch.sh \
 && /bin/echo 'disabled)' >> /var/xdebugswitch.sh \
 && /bin/echo     'phpdismod xdebug' >> /var/xdebugswitch.sh \
 && /bin/echo     'echo "Xdebug is Disabled"' >> /var/xdebugswitch.sh \
 && /bin/echo ';;' >> /var/xdebugswitch.sh \
 && /bin/echo 'esac' >> /var/xdebugswitch.sh \
 && chmod a+x /var/xdebugswitch.sh \
 && phpdismod xdebug

EXPOSE 9000

#Instalación de Composer
RUN php -r "readfile('https://getcomposer.org/installer');" | php -- --install-dir=/usr/local/bin --filename=composer

#Instalación de PHPUnit
RUN wget https://phar.phpunit.de/phpunit-5.7.phar \
 && chmod +x phpunit-5.7.phar \
 && mv phpunit-5.7.phar /usr/local/bin/phpunit

#NodeJS
RUN curl -sL https://deb.nodesource.com/setup_6.x | bash - \
 && apt-get install -y nodejs

#Yarn
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
 && /bin/echo -e "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list \
 && apt-get update \
 && apt-get install -y yarn

#Bower, gulp, grunt
RUN yarn global add bower \
 && yarn global add gulp \
 && yarn global add grunt \
 && /bin/echo '{ "allow_root": true }' > /root/.bowerrc

#MySQL Client
RUN /bin/echo "deb http://repo.mysql.com/apt/ubuntu/ xenial mysql-5.7" >> /etc/apt/sources.list.d/mysql.list \
 && /bin/echo "deb-src http://repo.mysql.com/apt/ubuntu/ xenial mysql-5.7" >> /etc/apt/sources.list.d/mysql.list \
 && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 5072E1F5 \
 && apt-get update \
 && apt-get install -y mysql-community-client

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
