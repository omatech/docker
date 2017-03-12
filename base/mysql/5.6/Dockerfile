FROM omatech/base

#Actualizamos el sistema
RUN apt-get update && apt-get upgrade -y

#Instalación de MySQL-5.6
RUN groupadd mysql --gid 500 \
 && useradd mysql --uid 500 --gid 500 --home-dir /var/lib/mysql --shell /bin/false --comment "MySQL Server,,," \
 && /bin/echo mysql-community-server mysql-community-server/root-pass password 'root' | debconf-set-selections \
 && /bin/echo mysql-community-server mysql-community-server/re-root-pass password 'root' | debconf-set-selections \
 && /bin/echo "deb http://repo.mysql.com/apt/ubuntu/ trusty mysql-5.6" >> /etc/apt/sources.list.d/mysql.list \
 && /bin/echo "deb-src http://repo.mysql.com/apt/ubuntu/ trusty mysql-5.6" >> /etc/apt/sources.list.d/mysql.list \
 && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 5072E1F5 \
 && apt-get remove -y mysql-client mysql-server libmysqlclient-dev mysql-common \
 && /bin/echo 'Package: *' > "/etc/apt/preferences.d/mysql" \
 && /bin/echo 'Pin: origin "repo.mysql.com"' >> "/etc/apt/preferences.d/mysql" \
 && /bin/echo 'Pin-Priority: 999' >> "/etc/apt/preferences.d/mysql" \
 && apt-get update \
 && apt-get install -y mysql-community-server \
 && sed -i 's/\/lib\/init\/apparmor-profile-load usr.sbin.mysqld/#\/lib\/init\/apparmor-profile-load usr.sbin.mysqld/' /etc/init.d/mysql \
 && sed -i 's/^\(bind-address\s.*\)/# \1/' /etc/mysql/my.cnf \
 && sed -i '/^\(log-error\s.*\)/a slow_query_log  = \/var\/log\/mysql\/mysql-slow.log' /etc/mysql/my.cnf \
 && sed -i '/^\(slow_query_log\s.*\)/a long_query_time = 2' /etc/mysql/my.cnf \
 && sed -i '/^\(long_query_time\s.*\)/a ft_min_word_len = 3' /etc/mysql/my.cnf

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
