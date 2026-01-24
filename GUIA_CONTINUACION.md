# 📋 Guía de Continuación - Repaso Cisco Tema 6

Este documento sirve como hoja de ruta para retomar el trabajo de documentación y estudio del **Tema 6: Explotación de Vulnerabilidades Basadas en Aplicaciones**.

## 📌 Estado Actual y Arquitectura
El proyecto sigue una estructura de "Hub Centralizado" diseñada para ser profesional, escalable y lista para GitHub:

### 📂 Organización de Directorios
*   **`assets/img/`**: Contiene todos los recursos visuales (mapas mentales). Separarlos de la raíz evita el desorden y facilita la gestión de rutas en Markdown.
*   **`labs/`**: Actúa como una biblioteca de referencia técnica. Aquí residen copias de los laboratorios prácticos originales.
*   **`Resumen_Repaso_Tema6.md`**: Es la "fuente de la verdad". Mezcla la teoría de Cisco con enlaces relativos a los recursos visuales y a los pasos técnicos de los laboratorios.

### 🔗 Lógica de Enlazado
*   **Teoría -> Lab**: En lugar de copiar todo el procedimiento técnico en el resumen, ponemos enlaces a los archivos en `labs/`. Esto mantiene el documento de repaso ágil y legible.
*   **Teoría -> Assets**: Las imágenes se referencian usando rutas relativas `./assets/img/...` para asegurar que se vean correctamente tanto en VS Code como en GitHub.

---

## 🚀 Tareas Pendientes

### 1. Documentación Teórica (NetAcad)
- [ ] **6.6: Explotando Vulnerabilidades de Autorización:**
    - Estudiar IDOR (Insecure Direct Object Reference).
    - Escalada de Privilegios (Horizontal vs Vertical).
- [ ] **6.7: Vulnerabilidades de Configuración y Componentes:**
    - Seguridad en cabeceras HTTP.
    - Uso de componentes con vulnerabilidades conocidas (CVEs).

### 2. Integración de Laboratorios
- [ ] Revisar si hay ejercicios de **IDOR** en los labs de WebGoat o Juice Shop para referenciarlos en la sección 6.6.
- [ ] Buscar ejemplos de **Escalada de Privilegios** documentados.

### 3. Recursos Visuales
- [ ] Generar Mapa Mental para la sección 6.6 (Autorización).
- [ ] Generar Mapa Mental para la sección 6.7.

### 4. Finalización
- [ ] Repaso general del documento `Resumen_Repaso_Tema6.md`.
- [ ] `git push` al repositorio remoto (una vez configurado).

---
**Notas de la IA Mentor:** Hemos dejado el entorno muy bien organizado. Para la próxima sesión, el primer paso lógico es abrir NetAcad en la sección 6.6 y empezar a extraer los conceptos clave de IDOR para el resumen.
