FROM php:8.2-apache

# Instalación de ModSecurity y OpenSSL para los certificados
RUN apt-get update && apt-get install -y \
    libapache2-mod-security2 \
    openssl \
    && apt-get clean

# 1. Generar certificados SSL
RUN mkdir -p /etc/apache2/ssl && \
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/apache2/ssl/apache.key \
    -out /etc/apache2/ssl/apache.crt \
    -subj "/C=ES/ST=Castellon/L=Castellon/O=M4raa/OU=IT/CN=www.m4raa.com"

# 2. Copiar todas las configuraciones
COPY conf/m4raa.conf /etc/apache2/sites-available/m4raa.conf
COPY conf/hardening.conf /etc/apache2/conf-available/hardening.conf
COPY conf/security-headers.conf /etc/apache2/conf-available/security-headers.conf
COPY conf/modsecurity.conf /etc/modsecurity/modsecurity.conf

# 3. Habilitar módulos (incluyendo SSL) y configuraciones
RUN a2enmod headers rewrite unique_id security2 ssl && \
    a2enconf hardening security-headers && \
    a2dissite 000-default.conf && \
    a2ensite m4raa.conf

# 4. Configurar logs de ModSecurity
RUN touch /var/log/apache2/modsec_audit.log && \
    chown www-data:www-data /var/log/apache2/modsec_audit.log

# 5. Copiar contenido y asegurar permisos correctos
COPY content/ /var/www/html/
RUN chown -R www-data:www-data /var/www/html && \
    chmod -R 755 /var/www/html

EXPOSE 80 443