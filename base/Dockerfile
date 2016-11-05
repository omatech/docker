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
RUN apt-get install -y vim nano curl wget gdebi git zsh \
 && wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh || true \
 && chsh -s /bin/zsh

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
