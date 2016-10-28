FROM omatech/base

#Actualizamos el sistema
RUN apt-get update && apt-get upgrade -y

#Instalación de MySQL-5.5
RUN /bin/echo mysql-server mysql-server/root_password password 'root' | debconf-set-selections \
 && /bin/echo mysql-server mysql-server/root_password_again password 'root' | debconf-set-selections \
 && apt-get install -y mysql-server \
 && sed -i 's/^\(bind-address\s.*\)/# \1/' /etc/mysql/my.cnf  \
 && /bin/echo -e "[program:mysql]\ncommand=/usr/bin/mysqld_safe\n" >> /etc/supervisor/conf.d/supervisord.conf

EXPOSE 3306

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
