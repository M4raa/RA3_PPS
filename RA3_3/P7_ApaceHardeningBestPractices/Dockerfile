FROM php:8.2-apache

# 1. Instalación de ModSecurity y OpenSSL
RUN apt-get update && apt-get install -y \
    libapache2-mod-security2 \
    openssl \
    && apt-get clean

# 2. Generar certificados SSL para el dominio
RUN mkdir -p /etc/apache2/ssl && \
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/apache2/ssl/apache.key \
    -out /etc/apache2/ssl/apache.crt \
    -subj "/C=ES/ST=Castellon/L=Castellon/O=M4raa/OU=IT/CN=www.m4raa.com"

# 3. Copiar configuraciones
COPY conf/m4raa.conf /etc/apache2/sites-available/m4raa.conf
COPY conf/hardening.conf /etc/apache2/conf-available/hardening.conf
COPY conf/security-headers.conf /etc/apache2/conf-available/security-headers.conf
COPY conf/modsecurity.conf /etc/modsecurity/modsecurity.conf

# 4. Habilitar módulos y aplicar configuraciones
RUN a2enmod headers rewrite unique_id security2 ssl && \
    a2enconf hardening security-headers && \
    a2dissite 000-default.conf && \
    a2ensite m4raa.conf

# 5. Configurar logs y contenido
RUN touch /var/log/apache2/modsec_audit.log && \
    chown www-data:www-data /var/log/apache2/modsec_audit.log

COPY content/ /var/www/html/
RUN chown -R www-data:www-data /var/www/html && \
    chmod -R 755 /var/www/html

EXPOSE 80 443