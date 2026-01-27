![Version](https://img.shields.io/badge/version-v2026.1.27-blue)
![Docker](https://img.shields.io/badge/docker-stable-blue)
![Apache](https://img.shields.io/badge/apache-2.4.54-red)
![Nginx](https://img.shields.io/badge/nginx-latest-green)
![PHP](https://img.shields.io/badge/php-8.4.16-777bb4)
![License](https://img.shields.io/badge/license-MIT-yellow)

# Puesta en Producción Segura (PPS) - Hardening de Servidores Web

Este repositorio centraliza el despliegue de soluciones de seguridad y endurecimiento (hardening) para servidores web Apache y Nginx mediante 
el uso de contenedores Docker. El proyecto recorre desde la configuración de cabeceras de seguridad básicas hasta la implementación de un WAF 
profesional y estrategias de mitigación de ataques DDoS.

---

## 1. Estrategia de Capas Incrementales
Una característica fundamental de este proyecto es la **arquitectura en cascada**. La mayoría de los contenedores
se construyen tomando como base la imagen de la práctica anterior (`FROM m4raa/pps:prX`), exceptuando la práctica de Nginx.

Esta metodología permite:
* **Acumulación de seguridad**: Cada nueva capa añade protecciones sin perder las configuraciones críticas previas (SSL, CSP, HSTS, etc.).
* **Modularidad**: Facilita la identificación de qué nivel de seguridad está bloqueando una amenaza específica.
* **Optimización**: Reutiliza las capas de imagen existentes para reducir los tiempos de construcción y el almacenamiento.

---

## 2. Árbol de Directorios
La jerarquía del proyecto está organizada por bloques de actividades según la estructura visual del repositorio:

```text
.
├── RA3_1/
│   ├── P1_CSP/                   # Hardening base: CSP, HSTS y SSL
│   ├── P2_WebApplicationFirewall/# Implementación de ModSecurity WAF
│   ├── P3_OWASP/                 # Integración de OWASP Core Rule Set
│   ├── P4_DDOS/                  # Mitigación DoS con mod_evasive
│   └── P5_NGINX/                 # Hardening en Nginx (Práctica Extra)
├── RA3_2/
│   └── P6_Certificados/          # Gestión avanzada de certificados SSL
├── RA3_3/
│   └── P7_ApaceHardeningBestP... # Apache Hardening Best Practices
└── README.md                     # Documentación general
```

---

## 3. Gestión de Puertos
Para garantizar que todos los servicios puedan ejecutarse de forma simultánea sin conflictos en la máquina host, 
se ha implementado un esquema de puertos a partir del rango **40000+**:

| Práctica | HTTP (Host) | HTTPS (Host) | Contenedor |
| :--- | :--- | :--- | :--- |
| **P1** | 41080 | 41443 | `pps_pr1` |
| **P2** | 42080 | 42443 | `pps_pr2` |
| **P3** | 43080 | 43443 | `pps_pr3` |
| **P4** | 44080 | 44443 | `pps_pr4` |
| **P5** | 45080 | 45443 | `pps_pr5` |
| **P6** | 46080 | 46443 | `pps_pr6` |
| **P7** | 47080 | 47443 | `pps_pr7` |

---

## 4. Resumen de Prácticas Realizadas

| Práctica | Descripción Técnica | Docker Tag |
| :--- | :--- | :--- |
| **P1** | **Hardening Base**: Configuración de CSP, HSTS y cifrado SSL. | `m4raa/pps:pr1` |
| **P2** | **WAF**: Activación del firewall ModSecurity en modo bloqueo activo. | `m4raa/pps:pr2` |
| **P3** | **OWASP**: Aplicación de reglas avanzadas mediante el Core Rule Set. | `m4raa/pps:pr3` |
| **P4** | **Anti-DDoS**: Mitigación de ráfagas de tráfico con `mod_evasive`. | `m4raa/pps:pr4` |
| **P5** | **Nginx Extra**: Hardening, autenticación básica y procesamiento PHP. | `m4raa/pps:pr5` |
| **P6** | **Certificados**: Instalación y gestión de certificados digitales SSL. | `m4raa/pps:pr6` |
| **P7** | **Best Practices**: Securización integral siguiendo estándares industriales. | `m4raa/pps:pr7` |

---

## 5. Enlaces y Repositorios
* **Docker Hub**: [m4raa/pps - Repositorio de Imágenes](https://hub.docker.com/repository/docker/m4raa/pps/tags)
* **GitHub**: [m4raa/pps-hardening - Código Fuente](https://github.com/m4raa/pps-hardening)