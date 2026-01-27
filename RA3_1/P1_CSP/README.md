# Práctica 3.1.1: Apache Hardening - CSP, HSTS y SSL

## 1. Estructura de archivos
El proyecto debe mantener esta jerarquía exacta para que el despliegue sea exitoso:

```text
P1_CSP/
├── assets/
│   └── image.png
├── conf/
│   └── security-headers.conf
├── ssl/
│   ├── apache-selfsigned.crt
│   └── apache-selfsigned.key
├── Dockerfile
└── README.md
```

## 2. Configuración de seguridad (security-headers.conf)
Define las políticas para mitigar ataques XSS y forzar el uso de conexiones cifradas.

```apache
<IfModule mod_headers.c>
    # CSP: Solo permite carga de recursos del mismo dominio
    Header always set Content-Security-Policy "default-src 'self';"

    # HSTS: Obliga al navegador a usar HTTPS durante 2 años
    Header always set Strict-Transport-Security "max-age=63072000; includeSubDomains; preload"
</IfModule>
```

## 3. Dockerfile (Imagen Base)
Configura el servidor Apache, instala los certificados y aplica el hardening inicial.

```dockerfile
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
```

## 4. Despliegue y uso

1. **Construir la imagen**:
   ```bash
   docker build -t m4raa/pps:pr1 .
   ```

2. **Subir a Docker Hub**:
   ```bash
   docker push m4raa/pps:pr1
   ```

3. **Ejecutar el contenedor**:
   ```bash
   docker run -d -p 41080:80 -p 41443:443 --name pps_pr1 m4raa/pps:pr1
   ```

## 5. Validación
Verifica que las cabeceras estén activas con el siguiente comando:

```bash
curl -k -I https://localhost:41443
```

**Resultado esperado**: Deben aparecer las líneas `Content-Security-Policy` y `Strict-Transport-Security`.

**Evidencia**:
![Validación](assets/image.png)

## 6. URL Docker Hub
[m4raa/pps:pr1](https://hub.docker.com/repository/docker/m4raa/pps/tags/pr1)