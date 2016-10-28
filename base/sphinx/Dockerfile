FROM omatech/base

#Actualizamos el sistema
RUN apt-get update && apt-get upgrade -y

#Instalación de sphinx
RUN wget -P /tmp http://sphinxsearch.com/files/sphinxsearch_2.2.11-release-1~trusty_amd64.deb \
 && gdebi -n /tmp/sphinxsearch_2.2.11-release-1~trusty_amd64.deb

#Configuración de sphinx y cron
RUN sed -i "s|START=no|START=yes|g" /etc/default/sphinxsearch \
 && /bin/echo -e '*/5 * * * * indexer --all --rotate' >> /etc/cron.d/sphinx-cron \
 && chmod 0644 /etc/cron.d/sphinx-cron \
 && /bin/echo -e "[program:crontab]\ncommand=cron -f\n" >> /etc/supervisor/conf.d/supervisord.conf \
 && /bin/echo -e "#!/bin/bash\n/usr/bin/indexer --rotate --all\n/usr/bin/searchd -c /etc/sphinxsearch/sphinx.conf --nodetach\n" >> /var/startup.sh \
 && /bin/echo -e '[program:sphinx]\ncommand=bash /var/startup.sh' >> /etc/supervisor/conf.d/supervisord.conf

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
