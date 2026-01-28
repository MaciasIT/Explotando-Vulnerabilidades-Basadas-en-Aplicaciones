FROM kalilinux/kali-rolling

# Metadatos
LABEL maintainer="Antigravity & Michel Macias"
LABEL description="Contenedor de ataque Kali Linux con herramientas pre-instaladas para el Lab Tema 6"

# Evitar preguntas interactivas durante la instalación
ENV DEBIAN_FRONTEND=noninteractive

# 1. Actualizar repositorios e instalar herramientas básicas
# Agrupamos los comandos en un solo RUN para reducir capas de la imagen (Best Practice)
RUN apt-get update && apt-get install -y \
    # Herramientas de Red Básicas
    iputils-ping \
    net-tools \
    curl \
    wget \
    iproute2 \
    # Editores
    vim \
    nano \
    # ---- Herramientas de Hacking (Tema 6) ----
    # 1. Escaneo y Reconocimiento
    nmap \
    whatweb \
    gobuster \
    # 2. Ataques Web y Bases de Datos
    sqlmap \
    nikto \
    # 3. Fuerza Bruta y Diccionarios
    hydra \
    seclists \
    wordlists \
    # 4. Utilidades Varias (Reverse Shells, etc)
    netcat-traditional \
    && \
    # Limpieza para aligerar la imagen
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Configurar alias útil para ll
RUN echo "alias ll='ls -alF'" >> /root/.bashrc

# Directorio de trabajo por defecto
WORKDIR /root

# Mantener el contenedor vivo infinitamente para poder entrar por terminal
CMD ["tail", "-f", "/dev/null"]
