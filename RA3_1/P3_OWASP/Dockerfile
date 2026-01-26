FROM m4raa/pps:pr2

# 2. Instalamos git para bajar las reglas de SpiderLabs
RUN apt-get update && apt-get install -y git

# 3. Clonamos el repositorio indicado en la práctica
RUN git clone https://github.com/SpiderLabs/owasp-modsecurity-crs.git /etc/modsecurity/owasp-modsecurity-crs

# 4. Preparamos el archivo de configuración de las reglas
RUN mv /etc/modsecurity/owasp-modsecurity-crs/crs-setup.conf.example /etc/modsecurity/owasp-modsecurity-crs/crs-setup.conf

# 5. Sustituimos el archivo de configuración de Apache por el nuestro
COPY conf/security2.conf /etc/apache2/mods-available/security2.conf

# 6. Limpiamos para reducir el tamaño de la imagen
RUN apt-get purge -y git && apt-get autoremove -y && rm -rf /var/lib/apt/lists/*

EXPOSE 80 443