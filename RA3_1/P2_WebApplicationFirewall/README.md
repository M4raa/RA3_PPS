# Práctica 3.1.2: Web Application Firewall (ModSecurity)

## 1. Estructura de archivos
El proyecto debe mantener esta jerarquía para que los comandos funcionen:

```text
P2_WebApplicationFirewall/
├── assets/
│   └── image.png
├── Dockerfile
└── README.md
```

## 2. Descripción
Esta práctica implementa un **WAF (Web Application Firewall)** sobre la base segura de la práctica anterior.

* **Imagen Base**: Se utiliza `m4raa/pps:pr1` (Estrategia de capas/cascada).
* **Módulo**: `mod_security2`.
* **Configuración**: Se activa el `SecRuleEngine` para pasar de modo detección a modo bloqueo activo.

## 3. Dockerfile
Configuración para instalar y activar el cortafuegos de aplicaciones:

```dockerfile
FROM m4raa/pps:pr1

# 1. Instalar ModSecurity y limpiar caché de apt para reducir tamaño
RUN apt-get update && apt-get install -y libapache2-mod-security2 && \
    rm -rf /var/lib/apt/lists/*

# 2. Configurar ModSecurity: cambiar de "DetectionOnly" a "On" (bloqueo activo)
RUN cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf && \
    sed -i 's/SecRuleEngine DetectionOnly/SecRuleEngine On/' /etc/modsecurity/modsecurity.conf

# 3. Asegurar la activación del módulo en Apache
RUN a2enmod security2
```

## 4. Despliegue y uso

1. **Construir la imagen**:
   ```bash
   docker build -t m4raa/pps:pr2 .
   ```

2. **Subir a Docker Hub**:
   ```bash
   docker push m4raa/pps:pr2
   ```

3. **Ejecutar el contenedor**:
   ```bash
   docker run -d -p 42080:80 -p 42443:443 --name pps_pr2 m4raa/pps:pr2
   ```

## 5. Validación
Para verificar que el WAF está filtrando ataques, se lanza una petición maliciosa de inyección de archivos:

```bash
curl -k -I "https://localhost:42443/index.html?exec=/etc/passwd"
```

**Resultado esperado**: El servidor debe devolver un **HTTP/1.1 403 Forbidden**. Esto confirma que ModSecurity ha detectado la amenaza y ha bloqueado el acceso.

**Evidencia de bloqueo**:
![Validación WAF](assets/image.png)

## 6. URL Docker Hub
[m4raa/pps:pr2](https://hub.docker.com/repository/docker/m4raa/pps/tags/pr2)