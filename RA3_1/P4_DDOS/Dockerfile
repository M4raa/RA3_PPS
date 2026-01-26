FROM m4raa/pps:pr3

# 2. Instalamos el módulo mod_evasive
RUN apt-get update && apt-get install -y libapache2-mod-evasive

# 3. Creamos el directorio de logs y asignamos permisos a www-data
RUN mkdir -p /var/log/mod_evasive && \
    chown -R www-data:www-data /var/log/mod_evasive

# 4. Copiamos nuestra configuración personalizada
COPY conf/evasive.conf /etc/apache2/mods-available/evasive.conf

# 5. Limpiamos la caché de apt para reducir el tamaño
RUN rm -rf /var/lib/apt/lists/*

EXPOSE 80 443