FROM omatech/nginx

#Actualizamos el sistema
RUN apt-get update && apt-get upgrade -y

#Instalación de PHP 5.5
RUN /bin/echo "deb http://ppa.launchpad.net/ondrej/php/ubuntu trusty main" >> /etc/apt/sources.list.d/php.list \
 && /bin/echo "deb-src http://ppa.launchpad.net/ondrej/php/ubuntu trusty main" >> /etc/apt/sources.list.d/php.list \
 && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 14AA40EC0831756756D7F66C4F4EA0AAE5267A6C \
 && apt-get update \
 && apt-get install -y php5.5 php5.5-fpm php5.5-mysql php5.5-dom php5.5-mcrypt php5.5-mbstring php5.5-zip php5.5-gd php5.5-memcached \
 && apt-get install -y php5.5-xdebug \
 && /bin/echo -e "[program:fpm]\ncommand=/usr/sbin/php-fpm5.5 --nodaemonize\n" >> /etc/supervisor/conf.d/supervisord.conf \
 && mkdir -p /run/php \
 && sed -i "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/" /etc/php/5.5/fpm/php.ini \
 && /bin/echo -e "fastcgi_read_timeout 7200s;" >> /etc/nginx/fastcgi_params

#Instalación de Composer
RUN php -r "readfile('https://getcomposer.org/installer');" | php -- --install-dir=/usr/local/bin --filename=composer

#Instalación de PHPUnit
RUN wget https://phar.phpunit.de/phpunit-old.phar \
 && chmod +x phpunit-old.phar \
 && mv phpunit-old.phar /usr/local/bin/phpunit

EXPOSE 9000

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord"]
