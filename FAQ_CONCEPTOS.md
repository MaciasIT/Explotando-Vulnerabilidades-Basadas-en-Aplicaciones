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
*(Documento en constante actualización según avancemos en el repaso)*
