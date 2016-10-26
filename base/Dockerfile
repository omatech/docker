FROM ubuntu:trusty

#Actualizamos el sistema
RUN apt-get update && apt-get upgrade -y

#Instalación de Supervisor
RUN apt-get install -y supervisor \
 && mkdir -p /var/log/supervisor \
 && /bin/echo -e "[supervisord]\nnodaemon=true\n" > /etc/supervisor/conf.d/supervisord.conf

#Instalación de OpenSSH
RUN apt-get install -y openssh-server \
 && mkdir -p /var/run/sshd \
 && sed -i "$ a UseDNS no" /etc/ssh/sshd_config \
 && /bin/echo -e "[program:sshd]\ncommand=/usr/sbin/sshd -D\n" >> /etc/supervisor/conf.d/supervisord.conf \
 && sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config \
 && sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

EXPOSE 22

#Instalación de Utilidades
RUN apt-get install -y vim curl wget

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord"]