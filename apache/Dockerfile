FROM ubuntu

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get -y install apache2 php libapache2-mod-php php-mysql php-curl php-xml php-memcached

RUN DEBIAN_FRONTEND=noninteractive apt-get install -y tzdata
RUN dpkg --configure -a

WORKDIR /var/www/html/

COPY simple-lamp/ ./

RUN chown -R www-data:www-data ./uploads

CMD apachectl -D FOREGROUND