FROM omatech/base

#Actualizamos el sistema
RUN apt-get update && apt-get upgrade -y

#Instalación de Apache2
RUN /bin/echo "deb http://ppa.launchpad.net/ondrej/apache2/ubuntu trusty main" >> /etc/apt/sources.list.d/apache.list \
 && /bin/echo "deb-src http://ppa.launchpad.net/ondrej/apache2/ubuntu trusty main" >> /etc/apt/sources.list.d/apache.list \
 && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 14AA40EC0831756756D7F66C4F4EA0AAE5267A6C \
 && apt-get update \
 && apt-get install -y apache2 \
 && mkdir -p /var/lock/apache2 /var/run/apache2 \
 && sed -i "s|#AddDefaultCharset UTF-8|AddDefaultCharset UTF-8|g" /etc/apache2/conf-enabled/charset.conf \
 && /bin/echo -e "[program:apache2]\ncommand=/bin/bash -c 'source /etc/apache2/envvars && exec /usr/sbin/apache2 -D FOREGROUND'\n" >> /etc/supervisor/conf.d/supervisord.conf \
 && /bin/echo -e "stdout_logfile=/dev/stdout\n" >> /etc/supervisor/conf.d/supervisord.conf \
 && /bin/echo -e "stdout_logfile_maxbytes=0\n" >> /etc/supervisor/conf.d/supervisord.conf \
 && a2enmod rewrite

EXPOSE 80
EXPOSE 443

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord"]
