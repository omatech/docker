FROM ubuntu:xenial

#Actualizamos el sistema
RUN apt-get update && apt-get upgrade -y

#tzdata
RUN ln -fs /usr/share/zoneinfo/Europe/Madrid /etc/localtime && dpkg-reconfigure -f noninteractive tzdata

#Locales
RUN locale-gen en_US.UTF-8 en_GB.UTF-8 es_ES.UTF-8 ca_ES.UTF-8 \
 && export LC_ALL="en_US.UTF-8" LANG="en_US.UTF-8" LANGUAGE="en_US-UTF-8" \
 && /bin/echo -e 'export LC_ALL="en_US.UTF-8" LANG="en_US.UTF-8" LANGUAGE="en_US-UTF-8"' >> /root/.bashrc

#Instalación de Supervisor
RUN apt-get install -y supervisor \
 && mkdir -p /var/log/supervisor \
 && /bin/echo -e "[supervisord]\nnodaemon=true\n" > /etc/supervisor/conf.d/supervisord.conf

#Instalación de OpenSSH
RUN apt-get install -y openssh-server \
 && mkdir -p /var/run/sshd \
 && sed -i "$ a UseDNS no" /etc/ssh/sshd_config \
 && /bin/echo -e "[program:sshd]\ncommand=/usr/sbin/sshd -D\n" >> /etc/supervisor/conf.d/supervisord.conf \
 && sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config \
 && sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

EXPOSE 22

#Instalación de Git
RUN /bin/echo "deb http://ppa.launchpad.net/git-core/ppa/ubuntu xenial main" >> /etc/apt/sources.list.d/git.list \
 && /bin/echo "deb-src http://ppa.launchpad.net/git-core/ppa/ubuntu xenial main" >> /etc/apt/sources.list.d/git.list \
 && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E1DD270288B4E6030699E45FA1715D88E1DF1F24 \
 && apt-get update \
 && apt-get install -y git

#Instalación de Utilidades
RUN apt-get install -y vim nano curl wget gdebi zsh traceroute zip subversion rsync sendmail-bin sendmail ncftp lynx openssl ca-certificates pdftk imagemagick mailutils htop monit build-essential poppler-utils libimage-exiftool-perl smartmontools iotop software-properties-common

#Instalación de ngrok
RUN wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip -P /tmp \
 && unzip /tmp/ngrok-stable-linux-amd64.zip -d /tmp \
 && chmod +x /tmp/ngrok \
 && mv /tmp/ngrok /usr/local/bin/ngrok

#Instalación de Oh My Zsh
RUN wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh || true \
 && sed -i 's/# DISABLE_UNTRACKED_FILES_DIRTY="true"/DISABLE_UNTRACKED_FILES_DIRTY="true"/g' /root/.zshrc \
 && sed -i 's/# export LANG=en_US.UTF-8/export LC_ALL="en_US.UTF-8" LANG="en_US.UTF-8" LANGUAGE="en_US-UTF-8"/g' /root/.zshrc \
 && chsh -s /bin/zsh

#Limpiamos temporales
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#Comando de arranque
ENTRYPOINT ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
