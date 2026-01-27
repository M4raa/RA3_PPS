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