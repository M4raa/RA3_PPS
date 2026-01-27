FROM m4raa/pps:pr1

# 1. Instalar ModSecurity y limpiar caché de apt para reducir tamaño
RUN apt-get update && apt-get install -y libapache2-mod-security2 && \
    rm -rf /var/lib/apt/lists/*

# 2. Configurar ModSecurity: cambiar de "DetectionOnly" a "On" (bloqueo activo)
RUN cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf && \
    sed -i 's/SecRuleEngine DetectionOnly/SecRuleEngine On/' /etc/modsecurity/modsecurity.conf

# 3. Asegurar la activación del módulo en Apache
RUN a2enmod security2