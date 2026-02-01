# Práctica 6: Implementación de SSL en Apache (Certs)

## 1. Estructura de archivos
El proyecto debe estar organizado de la siguiente forma para un despliegue correcto:

```text
P6_Certificados/
├── assets/
│   └── image.png
├── conf/
│   └── m4raa.conf
├── content/
│   └── index.html
├── Dockerfile
└── README.md
```

## 2. Configuración del Servidor (conf/m4raa.conf)
Define la redirección forzosa al puerto 443 y la ruta de los certificados para el dominio `www.m4raa.com`.

```apache
<VirtualHost *:80>
    ServerName www.m4raa.com
    Redirect permanent / https://www.m4raa.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName www.m4raa.com
    DocumentRoot /var/www/html

    SSLEngine on
    SSLCertificateFile /etc/apache2/ssl/apache.crt
    SSLCertificateKeyFile /etc/apache2/ssl/apache.key
    
    <Directory /var/www/html>
        AllowOverride None
        Require all granted
    </Directory>
</VirtualHost>
```

## 3. Dockerfile
Construcción de la imagen basada en Apache con generación automática de certificados SSL.

```dockerfile
FROM php:8.2-apache

RUN a2enmod ssl

RUN mkdir -p /etc/apache2/ssl && \
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/apache2/ssl/apache.key \
    -out /etc/apache2/ssl/apache.crt \
    -subj "/C=ES/ST=Castellon/L=Castellon/O=M4raa/OU=IT/CN=www.m4raa.com"

COPY conf/m4raa.conf /etc/apache2/sites-available/m4raa.conf
RUN a2dissite 000-default.conf && a2ensite m4raa.conf

COPY content/index.html /var/www/html/

EXPOSE 80 443
```

## 4. Despliegue y uso

### Paso 1: Configuración de DNS Local
Añada la siguiente línea al archivo de hosts de su sistema operativo:
`127.0.0.1 www.m4raa.com`

### Paso 2: Ejecución
1. **Construir la imagen**:
   ```bash
   docker build -t m4raa/pps:pr6 .
   ```

2. **Subir a Docker Hub**:
   ```bash
   docker push m4raa/pps:pr6
   ```

3. **Ejecutar el contenedor (Puertos 46080 y 46443)**:
   ```bash
   docker run -d -p 46080:80 -p 46443:443 --name pps_pr6 m4raa/pps:pr6
   ```

## 5. Validación
Al navegar a `https://www.m4raa.com:46443`, el servidor presentará el certificado auto-firmado y mostrará la página de confirmación.

**Evidencia de funcionamiento**:
![Validación SSL](assets/image.png)

## 6. URL Docker Hub
[m4raa/pps:pr6](https://hub.docker.com/repository/docker/m4raa/pps/tags/pr6)