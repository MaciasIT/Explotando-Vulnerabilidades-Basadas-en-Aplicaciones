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
*(Documento en constante actualización según avancemos en el repaso)*
