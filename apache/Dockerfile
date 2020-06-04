FROM ubuntu

USER root

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get -y install apache2 php libapache2-mod-php php-mysql php-curl php-xml php-memcached

RUN DEBIAN_FRONTEND=noninteractive apt-get install -y tzdata
RUN dpkg --configure -a

COPY simple-lamp/ /var/www/html/

RUN chown -R www-data:www-data /var/www/html/uploads

CMD apachectl -D FOREGROUND