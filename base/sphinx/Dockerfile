FROM omatech/base

#Actualizamos el sistema
RUN apt-get update && apt-get upgrade -y

#Instalación de sphinx
RUN apt-get install -y sphinx \
 && '*/5 * * * * indexer --all --rotate' >> /etc/cron.d/sphinx-cron \
 && chmod 0644 /etc/cron.d/sphinx-cron \
 && /bin/echo -e "[program:crontab]\ncommand=cron -f\n" >> /etc/supervisor/conf.d/supervisord.conf \
 && /bin/echo -e '[program:sphinx]\ncommand=/usr/bin/searchd -c /etc/sphinxsearch/sphinx.conf --nodetach "$@"\n' >> /etc/supervisor/conf.d/supervisord.conf

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord"]
