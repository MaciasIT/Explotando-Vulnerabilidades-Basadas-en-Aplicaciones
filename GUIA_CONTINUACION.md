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
- [x] **6.6: Explotando Vulnerabilidades de Autorización.** (Completado)
- [x] **6.7: Vulnerabilidades de Configuración y Componentes.** (Completado)
- [ ] **6.8: Web Services y Almacenamiento:**
    - [x] Borrador inicial de conceptos (SOAP, REST, Mass Assignment).
    - [ ] Verificar alineación exacta con el temario final de NetAcad (pendiente por glitch de acceso).

### 2. Integración de Laboratorios
- [x] Revisar si hay ejercicios de **IDOR** en los labs de WebGoat o Juice Shop. (Integrados ejemplos de Juice Shop)
- [x] Buscar ejemplos de **Escalada de Privilegios** documentados.

### 3. Recursos Visuales
- [x] Generar Mapa Mental para la sección 6.6 (Autorización).
- [x] Generar Mapa Mental para la sección 6.7.
- [x] **Generar Mapa Mental para la sección 6.8 (Web Services).**

### 4. Finalización
- [ ] Repaso general del documento `Resumen_Repaso_Tema6.md` para asegurar coherencia.
- [ ] `git push` al repositorio remoto (una vez configurado).

---
**Notas de la IA Mentor:** Hemos completado el borrador principal del Tema 6. La sección 6.8 está basada en conocimiento estándar de la industria y requiere una validación rápida contra el curso oficial cuando el acceso se restablezca. El siguiente paso creativo es generar el visual para la sección 6.8.
