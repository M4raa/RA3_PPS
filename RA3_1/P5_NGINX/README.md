# Práctica 5 EXTRA: Nginx Hardening (SSL, Auth y PHP)

## 1. Estructura de archivos
El proyecto debe estar organizado de la siguiente forma:

```text
P5_NGINX/
├── assets/
│   ├── image.png
│   └── image2.png
├── conf/
│   └── default.conf
├── content/
│   ├── index.html
│   └── index.php
├── Dockerfile
└── README.md
```

## 2. Configuración del Servidor (conf/default.conf)
Define el comportamiento de Nginx, aplicando hardening de firma de servidor y túneles SSL.

```nginx
server {
    listen 80;
    listen 443 ssl;
    server_name localhost;

    server_tokens off;

    ssl_certificate /etc/nginx/ssl/nginx.crt;
    ssl_certificate_key /etc/nginx/ssl/nginx.key;

    auth_basic "Acceso Restringido";
    auth_basic_user_file /etc/nginx/.htpasswd;

    root /var/www/html;
    index index.php index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

## 3. Dockerfile
Construcción de la imagen basada en Nginx oficial con soporte para PHP-FPM y seguridad reforzada.

```dockerfile
FROM nginx:latest

USER root

RUN apt-get update && apt-get install -y php-fpm openssl apache2-utils && apt-get clean

RUN mkdir -p /etc/nginx/ssl && \
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/nginx/ssl/nginx.key \
    -out /etc/nginx/ssl/nginx.crt \
    -subj "/C=ES/ST=Castellon/L=Castellon/O=M4raa/OU=TI/CN=localhost"

RUN htpasswd -bc /etc/nginx/.htpasswd admin admin123

RUN sed -i 's|listen = /run/php/php.*-fpm.sock|listen = 127.0.0.1:9000|' /etc/php/*/fpm/pool.d/www.conf

COPY conf/default.conf /etc/nginx/conf.d/default.conf
COPY content/ /var/www/html/

RUN chown -R www-data:www-data /var/www/html

EXPOSE 80 443

CMD service $(ls /etc/init.d/ | grep php) start && nginx -g "daemon off;"
```

## 4. Despliegue y uso

1. **Construir la imagen**:
   ```bash
   docker build -t m4raa/pps:pr5 .
   ```

2. **Subir a Docker Hub**:
   ```bash
   docker push m4raa/pps:pr5
   ```

3. **Ejecutar el contenedor (Puertos 45080 y 45443)**:
   ```bash
   docker run -d -p 45080:80 -p 45443:443 --name pps_pr5 m4raa/pps:pr5
   ```

## 5. Validación
Al acceder a `https://localhost:45443`, el navegador solicitará las credenciales configuradas (`admin` / `admin123`). Una vez autenticado, se mostrará la información de PHP validando el correcto funcionamiento del servidor.

**Evidencia de autenticación y cifrado**:
![Validación Auth](assets/image.png)

**Evidencia de procesamiento PHP**:
![Validación PHP](assets/image2.png)

## 6. URL Docker Hub
[m4raa/pps:pr5](https://hub.docker.com/repository/docker/m4raa/pps/tags/pr5)