FROM omatech/base

#Actualizamos el sistema
RUN apt-get update && apt-get upgrade -y

#Instalación de MySQL-5.6
RUN /bin/echo mysql-community-server mysql-community-server/root-pass password 'root' | debconf-set-selections \
 && /bin/echo mysql-community-server mysql-community-server/re-root-pass password 'root' | debconf-set-selections \
 && /bin/echo "deb http://repo.mysql.com/apt/ubuntu/ trusty mysql-5.6" >> /etc/apt/sources.list.d/mysql.list \
 && /bin/echo "deb-src http://repo.mysql.com/apt/ubuntu/ trusty mysql-5.6" >> /etc/apt/sources.list.d/mysql.list \
 && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys A4A9406876FCBD3C456770C88C718D3B5072E1F5 \
 && apt-get update \
 && apt-get install -y mysql-community-server \
 && sed -i 's/^\(bind-address\s.*\)/# \1/' /etc/mysql/my.cnf  \
 && /bin/echo -e "[program:mysql]\ncommand=/usr/bin/mysqld_safe\n" >> /etc/supervisor/conf.d/supervisord.conf

EXPOSE 3306

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord"]
