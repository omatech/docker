FROM omatech/base

#Actualizamos el sistema
RUN apt-get update && apt-get upgrade -y

#Instalación de MySQL-5.7
RUN groupadd mysql --gid 500 \
 && useradd mysql --uid 500 --gid 500 --home-dir /var/lib/mysql --shell /bin/false --comment "MySQL Server,,," \
 && /bin/echo mysql-community-server mysql-community-server/root-pass password 'root' | debconf-set-selections \
 && /bin/echo mysql-community-server mysql-community-server/re-root-pass password 'root' | debconf-set-selections \
 && /bin/echo "deb http://repo.mysql.com/apt/ubuntu/ xenial mysql-5.7" >> /etc/apt/sources.list.d/mysql.list \
 && /bin/echo "deb-src http://repo.mysql.com/apt/ubuntu/ xenial mysql-5.7" >> /etc/apt/sources.list.d/mysql.list \
 && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys A4A9406876FCBD3C456770C88C718D3B5072E1F5 \
 && apt-get update \
 && apt-get install -y mysql-community-server \
 && sed -i 's/\/lib\/apparmor\/profile-load usr.sbin.mysqld/#\/lib\/init\/apparmor-profile-load usr.sbin.mysqld/' /etc/init.d/mysql \
 && sed -i 's/^\(bind-address\s.*\)/# \1/' /etc/mysql/mysql.conf.d/mysqld.cnf \
 && sed -i '/^\(log-error\s.*\)/a slow_query_log  = \/var\/log\/mysql\/mysql-slow.log' /etc/mysql/mysql.conf.d/mysqld.cnf \
 && sed -i '/^\(slow_query_log\s.*\)/a long_query_time = 2' /etc/mysql/mysql.conf.d/mysqld.cnf \
 && sed -i '/^\(long_query_time\s.*\)/a ft_min_word_len = 3' /etc/mysql/mysql.conf.d/mysqld.cnf \
 && /bin/echo -e "#!/bin/bash\nchown -R mysql:mysql /var/lib/mysql\nchown -R mysql:mysql /var/lib/mysql-files\n/usr/bin/mysqld_safe" >> /var/startup.sh \
 && /bin/echo -e '[program:mysql]\ncommand=bash /var/startup.sh' >> /etc/supervisor/conf.d/supervisord.conf

EXPOSE 3306

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
