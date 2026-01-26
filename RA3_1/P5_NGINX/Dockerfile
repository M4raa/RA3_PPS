FROM nginx:latest

USER root

# 1. Instalación de PHP-FPM, OpenSSL y utilidades
RUN apt-get update && apt-get install -y php-fpm openssl apache2-utils && apt-get clean

# 2. Generación de certificados SSL
RUN mkdir -p /etc/nginx/ssl && \
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/nginx/ssl/nginx.key \
    -out /etc/nginx/ssl/nginx.crt \
    -subj "/C=ES/ST=Castellon/L=Castellon/O=M4raa/OU=TI/CN=localhost"

# 3. Credenciales (admin / admin123)
RUN htpasswd -bc /etc/nginx/.htpasswd admin admin123

# 4. Configurar PHP para escuchar en puerto TCP 9000
RUN sed -i 's|listen = /run/php/php.*-fpm.sock|listen = 127.0.0.1:9000|' /etc/php/*/fpm/pool.d/www.conf

# 5. Copia de archivos y permisos
# Asegúrate de tener index.html e index.php en tu carpeta
COPY conf/default.conf /etc/nginx/conf.d/default.conf
COPY content/ /var/www/html/

RUN chown -R www-data:www-data /var/www/html

EXPOSE 80 443

# 6. Arrancamos PHP y Nginx (daemon off para que no se apague)
CMD service $(ls /etc/init.d/ | grep php) start && nginx -g "daemon off;"