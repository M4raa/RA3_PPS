# Práctica 7: Apache Hardening Avanzado y WAF

## 1. Estructura de archivos
```text
P7_Hardening/
├── assets/
│   └── image.png
├── conf/
│   ├── hardening.conf
│   ├── m4raa.conf
│   ├── modsecurity.conf
│   └── security-headers.conf
├── content/
│   └── index.html
├── Dockerfile
└── README.md
```

## 2. Descripción Técnica
Esta práctica representa la culminación del despliegue seguro, integrando:
* **Firma de Servidor Personalizada**: Identidad oculta tras `M4raa-Secure-Gateway`.
* **Hardening de Directivas**: Desactivación de `Indexes`, `Trace` y limitación de métodos HTTP.
* **WAF Activo**: ModSecurity inspeccionando tráfico en tiempo real.
* **Cabeceras de Seguridad**: Implementación de CSP, HSTS y protección contra MIME-sniffing.

## 3. Configuración de VirtualHosts (conf/m4raa.conf)
```apache
<VirtualHost *:80>
    ServerName www.m4raa.com
    DocumentRoot /var/www/html
    <Directory /var/www/html>
        AllowOverride None
        Require all granted
    </Directory>
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

## 4. Despliegue y uso

1. **DNS Local**: Añada `127.0.0.1 www.m4raa.com` a su archivo `/etc/hosts`.

2. **Construir la imagen**:
   ```bash
   docker build -t m4raa/pps:pr7 .
   ```

3. **Subir a Docker Hub**:
   ```bash
   docker push m4raa/pps:pr7
   ```

4. **Ejecutar el contenedor (Puertos 47080 y 47443)**:
   ```bash
   docker run -d -p 47080:80 -p 47443:443 --name pps_pr7 m4raa/pps:pr7
   ```

## 5. Validación

### Bloqueo de Directory Traversal (WAF)
```bash
curl -I "http://www.m4raa.com:47080/?file=../../etc/passwd"
```
**Resultado**: HTTP 403 Forbidden.

### Inspección de Hardening y Cabeceras
```bash
curl -I -k https://www.m4raa.com:47443
```
**Resultado**: Se observa `Server: M4raa-Secure-Gateway` y todas las cabeceras de seguridad activas.

**Evidencia final**:
![Validación Hardening](assets/image.png)

## 6. URL Docker Hub
[m4raa/pps:pr7](https://hub.docker.com/repository/docker/m4raa/pps/tags/pr7)