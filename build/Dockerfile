# imagem default
FROM ubuntu:16.04
MAINTAINER Evandro Couto "vandocouto@gmail.com"

# Dir
WORKDIR /var/www/html/

# Run
RUN apt-get update && apt-get install python-software-properties software-properties-common -y && \
    add-apt-repository ppa:nginx/stable && \
    apt-get update && apt-get install nginx -y && \
    apt-get install vim passwd ifstat unzip net-tools curl nginx php7.0-fpm php7.0-cli php7.0-gd php7.0-dev php7.0-json php7.0-mysql php7.0-xml php7.0-xmlrpc php7.0-imap php7.0-mbstring php7.0-zip telnet wget openssh-server apt-utils php-redis -y && \
    rm -rf /var/lib/apt/lists/* && \
    chown www-data.www-data -R /var/www/html && \
    echo 'export LANG=C' >> /etc/profile && \
    rm /etc/localtime && \
    ln -s /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime && \
    mkdir /run/php && \
    rm /etc/nginx/sites-available/default && \
    rm /etc/php/7.0/fpm/pool.d/www.conf

# Dir
WORKDIR /

# COPY
COPY conf/default 	/etc/nginx/sites-available/
COPY conf/www.conf 	/etc/php/7.0/fpm/pool.d/
COPY scripts/start.sh 	/

# ports
EXPOSE 80
