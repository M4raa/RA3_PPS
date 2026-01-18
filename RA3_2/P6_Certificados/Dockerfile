FROM php:8.2-apache

RUN a2enmod ssl

RUN mkdir -p /etc/apache2/ssl && \
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/apache2/ssl/apache.key \
    -out /etc/apache2/ssl/apache.crt \
    -subj "/C=ES/ST=Castellon/L=Castellon/O=M4raa/OU=IT/CN=www.m4raa.com"

COPY m4raa.conf /etc/apache2/sites-available/m4raa.conf
RUN a2dissite 000-default.conf && a2ensite m4raa.conf

COPY index.html /var/www/html/

EXPOSE 80 443