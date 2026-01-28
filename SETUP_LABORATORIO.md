# 🧪 Guía de Despliegue del Laboratorio de Pentesting

Este documento detalla cómo desplegar el entorno de prácticas completo para el repaso del Tema 6. El entorno está automatizado utilizando **Docker** para garantizar que todos tenemos las mismas versiones y herramientas.

---

## 🏗️ Arquitectura del Laboratorio

El laboratorio consta de 4 máquinas virtuales ligeras (contenedores) conectadas en una red privada:

| Rol | Nombre Contenedor | IP Asignada (Red) | Puertos Expuestos (Localhost) | Tecnologías |
| :--- | :--- | :--- | :--- | :--- |
| **⚔️ Atacante** | `kali-attacker` | Dinámica (Rango 172.20.0.x) | N/A | Kali Linux, Nmap, Hydra, SqlMap... |
| **🎯 Víctima 1** | `juiceshop-target` | Dinámica | **3000** (`http://localhost:3000`) | NodeJS, Angular (Modern Web) |
| **🎯 Víctima 2** | `dvwa-target` | Dinámica | **8081** (`http://localhost:8081`) | PHP, MySQL (Classic Web) |
| **🎯 Víctima 3** | `webgoat-target` | Dinámica | **8080** (`http://localhost:8080`) | Java, Spring (Enterprise Web) |

---

## 🚀 Instrucciones de Inicio Rápido

### 1. Requisitos Previos
Asegúrate de tener instalados:
*   **Docker Desktop** (o Docker Engine en Linux).
*   **Git** (para clonar este repo, si no lo tienes).

### 2. Construir y Levantar el Entorno
Abre una terminal en la carpeta raíz del proyecto (donde está el archivo `docker-compose.yml`) y ejecuta:

```bash
docker compose up -d --build
```
*   `up`: Levanta los servicios.
*   `-d`: Detached mode (en segundo plano).
*   `--build`: Fuerza la construcción de nuestra imagen personalizada de Kali (tardará unos minutos la primera vez).

### 3. Verificar el Estado
Ejecuta:
```bash
docker ps
```
Deberías ver 4 contenedores en estado `Up`.

---

## ⚔️ Cómo Acceder a la Máquina Atacante (Kali)

Para realizar los laboratorios, trabajaremos **DENTRO** del contenedor Kali, ya que ahí están instaladas las herramientas (Nmap, Hydra, etc).

**Comando de Acceso:**
```bash
docker exec -it kali-attacker /bin/bash
```
*   Ahora tu terminal cambiará a `root@kali-attacker:/#`. ¡Estás dentro!

### 🛠️ Herramientas Pre-Instaladas
Una vez dentro de Kali, puedes verificar que tienes todo listo:
*   `nmap --version`
*   `hydra -h`
*   `sqlmap -h`
*   `wordlists` (están en `/usr/share/wordlists/`)

---

## 🎯 Configuración Inicial de los Objetivos

### 1. DVWA (Damn Vulnerable Web App)
Al entrar por primera vez a `http://localhost:8081`:
1.  Usuario/Pass por defecto: `admin` / `password`.
2.  Si ves errores de base de datos, haz clic en el botón **"Create / Reset Database"** abajo del todo.
3.  ¡Listo para atacar!

### 2. OWASP Juice Shop
Accede a `http://localhost:3000`. No requiere configuración previa.

### 3. WebGoat
Accede a `http://localhost:8080/WebGoat`. Requiere registrar un usuario nuevo (pon cualquiera) para empezar.

---

## 🛑 Detener el Laboratorio

Cuando termines, para apagar todo y liberar recursos:

```bash
docker compose down
```
*Nota: Los datos de DVWA y tus evidencias en la carpeta `/evidencias` son persistentes. El resto se reinicia.*
