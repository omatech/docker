FROM omatech/base

#Actualizamos el sistema
RUN apt-get update && apt-get upgrade -y

#Instalación de sphinx
RUN /bin/echo "deb http://ppa.launchpad.net/builds/sphinxsearch-rel22/ubuntu xenial main" >> /etc/apt/sources.list.d/apache.list \
 && /bin/echo "deb-src http://ppa.launchpad.net/builds/sphinxsearch-rel22/ubuntu xenial main" >> /etc/apt/sources.list.d/apache.list \
 && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 2FE7BA61B28B34B7A2B749A2B9D8946B16932B16 \
 && apt-get update \
 && apt-get install -y sphinxsearch

#Configuración de sphinx y cron
RUN sed -i "s|START=no|START=yes|g" /etc/default/sphinxsearch \
 && /bin/echo -e '*/5 * * * * indexer --all --rotate' >> /etc/cron.d/sphinx-cron \
 && chmod 0644 /etc/cron.d/sphinx-cron \
 && /bin/echo -e "[program:crontab]\ncommand=cron -f\n" >> /etc/supervisor/conf.d/supervisord.conf \
 && /bin/echo -e "#!/bin/bash\nsleep 3\n/usr/bin/indexer --rotate --all\n/usr/bin/searchd -c /etc/sphinxsearch/sphinx.conf --nodetach\n" >> /var/startup.sh \
 && /bin/echo -e '[program:sphinx]\ncommand=bash /var/startup.sh' >> /etc/supervisor/conf.d/supervisord.conf

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
