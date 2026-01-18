FROM m4raa/pps:pr1

# Instalar el módulo de seguridad ModSecurity
RUN apt-get update && apt-get install -y libapache2-mod-security2

# Configurar ModSecurity: cambiar de "DetectionOnly" a "On" (activo)
RUN cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf && \
    sed -i 's/SecRuleEngine DetectionOnly/SecRuleEngine On/' /etc/modsecurity/modsecurity.conf