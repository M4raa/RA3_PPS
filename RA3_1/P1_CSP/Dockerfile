FROM php:7.4-apache

# Desactivar autoindex para evitar fugas de información
RUN a2dismod autoindex -f

# Habilitar módulos necesarios para seguridad
RUN a2enmod headers ssl

# Copiar certificados digitales
COPY ssl/apache-selfsigned.crt /etc/ssl/certs/
COPY ssl/apache-selfsigned.key /etc/ssl/private/

# Aplicar cabeceras y configurar sitio SSL
COPY conf/security-headers.conf /etc/apache2/conf-available/security-headers.conf
RUN a2enconf security-headers

RUN sed -i 's|/etc/ssl/certs/ssl-cert-snakeoil.pem|/etc/ssl/certs/apache-selfsigned.crt|g' /etc/apache2/sites-available/default-ssl.conf && \
    sed -i 's|/etc/ssl/private/ssl-cert-snakeoil.key|/etc/ssl/private/apache-selfsigned.key|g' /etc/apache2/sites-available/default-ssl.conf

RUN a2ensite default-ssl

RUN echo "<h1>Práctica 1: Hardening Base OK</h1>" > /var/www/html/index.html

EXPOSE 80
EXPOSE 443