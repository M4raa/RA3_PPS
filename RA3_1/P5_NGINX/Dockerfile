FROM nginx:latest

USER root

# 1. Instalación de PHP-FPM, OpenSSL y utilidades
RUN apt-get update && apt-get install -y php-fpm openssl apache2-utils && apt-get clean

# 2. Generación de certificados SSL de 2048 bits
RUN mkdir -p /etc/nginx/ssl && \
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/nginx/ssl/nginx.key \
    -out /etc/nginx/ssl/nginx.crt \
    -subj "/C=ES/ST=Castellon/L=Castellon/O=M4raa/OU=TI/CN=localhost"

# 3. Credenciales de acceso (Usuario: admin / Clave: admin123)
RUN htpasswd -bc /etc/nginx/.htpasswd admin admin123

# 4. Configurar PHP-FPM para escucha TCP en puerto 9000
RUN sed -i 's|listen = /run/php/php.*-fpm.sock|listen = 127.0.0.1:9000|' /etc/php/*/fpm/pool.d/www.conf

# 5. Copia de configuraciones y contenido
COPY conf/default.conf /etc/nginx/conf.d/default.conf
COPY content/ /var/www/html/

RUN chown -R www-data:www-data /var/www/html

EXPOSE 80 443

# 6. Arranque de servicios
CMD service $(ls /etc/init.d/ | grep php) start && nginx -g "daemon off;"