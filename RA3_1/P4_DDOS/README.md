# Práctica 3.1.4: Protección contra ataques DDoS (mod_evasive)

## 1. Estructura de archivos
El proyecto debe mantener la siguiente jerarquía:

```text
P4_DDOS/
├── assets/
│   └── image.png
├── conf/
│   └── evasive.conf
├── Dockerfile
└── README.md
```

## 2. Configuración del módulo (conf/evasive.conf)
Define los umbrales para detectar y bloquear ráfagas de tráfico malicioso.

```apache
<IfModule mod_evasive24.c>
    # Tamaño de la tabla interna
    DOSHashTableSize    2048
    # Límite de peticiones a una misma página por segundo
    DOSPageCount        2
    # Límite de peticiones al sitio completo por segundo
    DOSSiteCount        50
    # Intervalos de conteo (segundos)
    DOSPageInterval     1
    DOSSiteInterval     1
    # Tiempo de bloqueo (segundos)
    DOSBlockingPeriod   10
    # Directorio para registros de bloqueo
    DOSLogDir           "/var/log/mod_evasive"
</IfModule>
```

## 3. Dockerfile
Implementación siguiendo la estrategia de capas sobre la base de OWASP CRS.

```dockerfile
FROM m4raa/pps:pr3

# 1. Instalación del módulo mod_evasive
RUN apt-get update && apt-get install -y libapache2-mod-evasive

# 2. Configuración del directorio de logs con permisos para Apache
RUN mkdir -p /var/log/mod_evasive && \
    chown -R www-data:www-data /var/log/mod_evasive

# 3. Aplicar configuración y activar el módulo explícitamente
COPY conf/evasive.conf /etc/apache2/mods-available/evasive.conf
RUN a2enmod evasive

# 4. Limpieza de seguridad
RUN rm -rf /var/lib/apt/lists/*

EXPOSE 80 443
```

## 4. Despliegue y uso

1. **Construir la imagen**:
   ```bash
   docker build -t m4raa/pps:pr4 .
   ```

2. **Subir a Docker Hub**:
   ```bash
   docker push m4raa/pps:pr4
   ```

3. **Ejecutar el contenedor**:
   ```bash
   docker run -d -p 44080:80 -p 44443:443 --name pps_pr4 m4raa/pps:pr4
   ```

## 5. Validación
Para verificar que el bloqueo funciona, se utiliza **ab** para generar tráfico masivo:

```bash
ab -n 100 -c 10 http://localhost:44080/index.html
```

**Resultado esperado**: El servidor devolverá un **HTTP/1.1 403 Forbidden** una vez superados los límites de peticiones establecidos en la configuración.

**Evidencia**:
![Validación DDoS](assets/image.png)

## 6. URL Docker Hub
[m4raa/pps:pr4](https://hub.docker.com/repository/docker/m4raa/pps/tags/pr4)