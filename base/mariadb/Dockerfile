FROM omatech/base

#Actualizamos el sistema
RUN apt-get update && apt-get upgrade -y

#Instalación de MariaDB
RUN /bin/echo mariadb-server mysql-server/root_password password 'root' | debconf-set-selections \
 && /bin/echo mariadb-server mysql-server/root_password_again password 'root' | debconf-set-selections \
 && /bin/echo "deb http://tedeco.fi.upm.es/mirror/mariadb/repo/10.1/ubuntu xenial main" >> /etc/apt/sources.list.d/mariadb.list \
 && /bin/echo "deb-src http://tedeco.fi.upm.es/mirror/mariadb/repo/10.1/ubuntu xenial main" >> /etc/apt/sources.list.d/mariadb.list \
 && apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0xF1656F24C74CD1D8 \
 && apt-get update \
 && apt-get install -y mariadb-server \
 && sed -i 's/^\(bind-address\s.*\)/# \1/' /etc/mysql/my.cnf  \
 && sed -i '/^\(log-error\s.*\)/a slow_query_log  = \/var\/log\/mysql\/mysql-slow.log' /etc/mysql/my.cnf \
 && sed -i '/^\(slow_query_log\s.*\)/a long_query_time = 2' /etc/mysql/my.cnf \
 && sed -i '/^\(long_query_time\s.*\)/a ft_min_word_len = 3' /etc/mysql/my.cnf \
 && /bin/echo -e "#!/bin/bash\nchown -R mysql:mysql /var/lib/mysql\nchown -R mysql:mysql /var/lib/mysql-files\n/usr/bin/mysqld_safe" >> /var/startup.sh \
 && /bin/echo -e '[program:mariadb]\ncommand=bash /var/startup.sh' >> /etc/supervisor/conf.d/supervisord.conf

EXPOSE 3306

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
