FROM omatech/base

#Actualizamos el sistema
RUN apt-get update && apt-get upgrade -y

#Instalación de MariaDB
RUN groupadd mysql --gid 500 \
 && useradd mysql --uid 500 --gid 500 --home-dir /var/lib/mysql --shell /bin/false --comment "MySQL Server,,," \
 && /bin/echo mariadb-server mysql-server/root_password password 'root' | debconf-set-selections \
 && /bin/echo mariadb-server mysql-server/root_password_again password 'root' | debconf-set-selections \
 && /bin/echo "deb http://tedeco.fi.upm.es/mirror/mariadb/repo/10.1/ubuntu xenial main" >> /etc/apt/sources.list.d/mariadb.list \
 && /bin/echo "deb-src http://tedeco.fi.upm.es/mirror/mariadb/repo/10.1/ubuntu xenial main" >> /etc/apt/sources.list.d/mariadb.list \
 && apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0xF1656F24C74CD1D8 \
 && apt-get update \
 && apt-get install -y mariadb-server

#Scripts de arranque
RUN /bin/echo -e "#!/bin/bash\nchown -R mysql:mysql /var/lib/mysql\nchown -R mysql:mysql /var/lib/mysql-files\n/usr/bin/mysqld_safe" >> /var/scripts/mysql_startup.sh \
 && chmod a+x /var/scripts/mysql_startup.sh \
 && /bin/echo -e '[program:mysql]\ncommand=bash /var/scripts/mysql_startup.sh' >> /etc/supervisor/conf.d/supervisord.conf \
 && /bin/echo -e "#!/bin/bash\nsleep 5\nmysql -e \"REVOKE ALL PRIVILEGES ON *.* FROM 'root'@'%'\" --user=root --password=root || true\nmysql -e \"DROP USER 'root'@'%'\" --user=root --password=root || true\nmysql -e \"GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '\$MYSQL_ROOT_PASSWORD' with grant option\" --user=root --password=root || true\nmysql -e \"FLUSH PRIVILEGES\" --user=root --password=root || true" > /var/scripts/mysql_init.sh \
 && chmod a+x /var/scripts/mysql_init.sh \
 && /bin/echo -e '[program:mysql_init]\nautorestart=false\ncommand=bash -c "/var/scripts/mysql_init.sh && tail -f /dev/null"' >> /etc/supervisor/conf.d/supervisord.conf

EXPOSE 3306

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
