FROM mysql/mysql-server

ENV MYSQL_ROOT_PASSWORD root

COPY simple_lamp.sql /docker-entrypoint-initdb.d/setup.sql

EXPOSE 3306