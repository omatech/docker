FROM omatech/base

#Actualizamos el sistema
RUN apt-get update && apt-get upgrade -y

#Instalación de MariaDB
RUN /bin/echo mariadb-server mysql-server/root_password password 'root' | debconf-set-selections \
 && /bin/echo mariadb-server mysql-server/root_password_again password 'root' | debconf-set-selections \
 && /bin/echo "deb http://tedeco.fi.upm.es/mirror/mariadb/repo/10.1/ubuntu trusty main" >> /etc/apt/sources.list.d/mariadb.list \
 && /bin/echo "deb-src http://tedeco.fi.upm.es/mirror/mariadb/repo/10.1/ubuntu trusty main" >> /etc/apt/sources.list.d/mariadb.list \
 && apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0xcbcb082a1bb943db \
 && apt-get update \
 && apt-get install -y mariadb-server \
 && sed -i 's/^\(bind-address\s.*\)/# \1/' /etc/mysql/my.cnf  \
 && /bin/echo -e "[program:mariadb]\ncommand=/usr/bin/mysqld_safe\n" >> /etc/supervisor/conf.d/supervisord.conf

EXPOSE 3306

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord"]
