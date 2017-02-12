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

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
