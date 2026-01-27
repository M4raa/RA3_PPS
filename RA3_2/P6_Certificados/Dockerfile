FROM php:8.2-apache

# 1. Habilitar el módulo SSL de Apache
RUN a2enmod ssl

# 2. Crear directorio y generar certificados auto-firmados para el dominio específico
RUN mkdir -p /etc/apache2/ssl && \
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/apache2/ssl/apache.key \
    -out /etc/apache2/ssl/apache.crt \
    -subj "/C=ES/ST=Castellon/L=Castellon/O=M4raa/OU=IT/CN=www.m4raa.com"

# 3. Configurar el VirtualHost personalizado
COPY conf/m4raa.conf /etc/apache2/sites-available/m4raa.conf
RUN a2dissite 000-default.conf && a2ensite m4raa.conf

# 4. Copiar contenido web
COPY content/index.html /var/www/html/

EXPOSE 80 443