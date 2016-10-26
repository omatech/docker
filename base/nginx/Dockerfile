FROM omatech/base

#Actualizamos el sistema
RUN apt-get update && apt-get upgrade -y

#Instalación de Nginx
RUN /bin/echo "deb http://ppa.launchpad.net/nginx/stable/ubuntu trusty main" >> /etc/apt/sources.list.d/nginx.list \
 && /bin/echo "deb-src http://ppa.launchpad.net/nginx/stable/ubuntu trusty main" >> /etc/apt/sources.list.d/nginx.list \
 && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 8B3981E7A6852F782CC4951600A6F0A3C300EE8C \
 && apt-get update \
 && apt-get install -y nginx \
 && /bin/echo -e "[program:nginx]\ncommand=/usr/sbin/nginx -g 'daemon off;'\n" >> /etc/supervisor/conf.d/supervisord.conf \
 && /bin/echo -e "stdout_logfile=/dev/stdout\n" >> /etc/supervisor/conf.d/supervisord.conf \
 && /bin/echo -e "stdout_logfile_maxbytes=0\n" >> /etc/supervisor/conf.d/supervisord.conf

EXPOSE 80
EXPOSE 443

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord"]
