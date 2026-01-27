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