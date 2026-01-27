# Práctica 3.1.3: OWASP Core Rule Set (CRS)

## 1. Estructura de archivos
El proyecto debe estar organizado de la siguiente forma:

```text
P3_OWASP/
├── assets/
│   └── image.png
├── conf/
│   └── security2.conf
├── Dockerfile
└── README.md
```

## 2. Configuración del WAF (conf/security2.conf)
Este archivo vincula el conjunto de reglas de OWASP con el motor de ModSecurity ya instalado.

```apache
<IfModule security2_module>
    # Carga la configuración base del motor
    Include /etc/modsecurity/modsecurity.conf

    # Carga el setup de OWASP CRS
    Include /etc/modsecurity/owasp-modsecurity-crs/crs-setup.conf

    # Carga el conjunto de reglas estándar
    Include /etc/modsecurity/owasp-modsecurity-crs/rules/*.conf
</IfModule>
```

## 3. Dockerfile (Estrategia de Capas)
Se construye sobre `m4raa/pps:pr2`. Instala las reglas oficiales de SpiderLabs y limpia herramientas temporales.

```dockerfile
FROM m4raa/pps:pr2

# 1. Instalar git para bajar las reglas
RUN apt-get update && apt-get install -y git

# 2. Clonar el repositorio oficial de reglas CRS
RUN git clone https://github.com/SpiderLabs/owasp-modsecurity-crs.git /etc/modsecurity/owasp-modsecurity-crs

# 3. Preparar el archivo de configuración de las reglas
RUN mv /etc/modsecurity/owasp-modsecurity-crs/crs-setup.conf.example /etc/modsecurity/owasp-modsecurity-crs/crs-setup.conf

# 4. Sustituir la configuración de carga de reglas en Apache
COPY conf/security2.conf /etc/apache2/mods-available/security2.conf

# 5. Limpieza de seguridad y reducción de tamaño
RUN apt-get purge -y git && apt-get autoremove -y && rm -rf /var/lib/apt/lists/*

EXPOSE 80 443
```

## 4. Despliegue y uso

1. **Construir la imagen**:
   ```bash
   docker build -t m4raa/pps:pr3 .
   ```

2. **Subir a Docker Hub**:
   ```bash
   docker push m4raa/pps:pr3
   ```

3. **Ejecutar el contenedor (Puertos 43080 y 43443)**:
   ```bash
   docker run -d -p 43080:80 -p 43443:443 --name pps_pr3 m4raa/pps:pr3
   ```

## 5. Validación
Para verificar que el WAF bloquea ataques complejos, intenta inyectar comandos de sistema:

```bash
curl -k -I "https://localhost:43443/index.html?exec=/bin/bash"
curl -k -I "https://localhost:43443/index.html?exec=/../../"
```

**Resultado esperado**: Ambas peticiones deben devolver un **HTTP/1.1 403 Forbidden**. Las reglas de OWASP CRS han detectado intentos de LFI (Local File Inclusion) y han denegado el acceso.

**Evidencia de validación**:
![Validación OWASP](assets/image.png)

## 6. URL Docker Hub
[m4raa/pps:pr3](https://hub.docker.com/repository/docker/m4raa/pps/tags/pr3)