FROM omatech/base

#Actualizamos el sistema
RUN apt-get update && apt-get upgrade -y

#Instalación de memcached
RUN apt-get install -y memcached \
 && /bin/echo -e "[program:memcached]\ncommand=/usr/bin/memcached -p 11211 -u memcache -m 64 -c 1024'\n" >> /etc/supervisor/conf.d/supervisord.conf

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord"]
