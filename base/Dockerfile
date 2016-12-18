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

#Instalación de Git
RUN /bin/echo "deb http://ppa.launchpad.net/git-core/ppa/ubuntu trusty main" >> /etc/apt/sources.list.d/git.list \
 && /bin/echo "deb-src http://ppa.launchpad.net/git-core/ppa/ubuntu trusty main" >> /etc/apt/sources.list.d/git.list \
 && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E1DD270288B4E6030699E45FA1715D88E1DF1F24 \
 && apt-get update \
 && apt-get install -y git 

#Instalación de Utilidades
RUN apt-get install -y vim nano curl wget gdebi zsh

#Instalación de Oh My Zsh
RUN wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh || true \
 && sed -i 's/plugins=(git)/plugins=()/g' /root/.zshrc \
 && chsh -s /bin/zsh

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
