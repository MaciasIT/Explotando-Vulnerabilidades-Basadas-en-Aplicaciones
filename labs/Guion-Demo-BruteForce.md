# Guion de Demo: Fuerza Bruta en DVWA (Medium)

**Objetivo del Video:** Demostrar que sanitizar entradas (Nivel Medio) evita el bypass de autenticación por SQL Injection, pero es inútil contra contraseñas débiles atacadas por Fuerza Bruta.

---

## 🏗️ 1. Preparación de la Escena
*   **Target:** DVWA en `http://localhost:8081`
*   **Atacante:** Terminal con `kali-attacker`
*   **Herramienta:** Hydra
*   **Estado Inicial:** DVWA Security Level = **Medium**

---

## 🎬 2. Flujo de la Demostración

### Escena 1: El intento fallido (SQL Injection)
*   **Acción:** Ir a la pestaña "Brute Force" en el navegador.
*   **Narrativa:** "Primero, intentamos un bypass clásico de autenticación que funcionaba en nivel bajo."
*   **Input en Web:**
    *   User: `admin' #`
    *   Pass: `123`
*   **Resultado Esperado:** **Fallo**. "Username and/or password incorrect."
*   **Explicación:** "Como vemos, el nivel Medio aplica `mysql_real_escape_string()`, lo que neutraliza nuestra comilla simple. La inyección SQL ya no es posible aquí."

### Escena 2: El análisis (View Source)
*   **Acción:** Clic en botón "View Source" (abajo a la derecha).
*   **Narrativa:** "Si miramos el código, vemos la protección contra inyecciones. Sin embargo, no vemos ningún mecanismo de bloqueo de cuenta, retardos artificiales (sleep) ni CAPTCHA."

### Escena 3: El ataque real (Fuerza Bruta)
*   **Acción:** Abrir terminal de Kali.
*   **Narrativa:** "La vulnerabilidad ahora reside en el factor humano: una contraseña débil. Usaremos Hydra para probar un diccionario contra el formulario."
*   **Comando (Hydra):**
    *(Nota: Asegúrate de actualizar la Cookie PHPSESSID en el comando)*
    
    ```bash
    hydra -l admin -P /tmp/pass.txt -s 80 dvwa-target http-get-form "/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login:H=Cookie\: security=medium; PHPSESSID=[TU_COOKIE_AQUI]:F=incorrect"
    ```
    
*   **Ejecución:** Lanzar el comando.
*   **Resultado Esperado:** Hydra encuentra la password `password` en segundos.

### Escena 4: Verificación
*   **Acción:** Volver al navegador. Loguearse con `admin` / `password`.
*   **Resultado:** "Welcome to the password protected area admin".

---

## 🏁 3. Conclusión para el Video
"Hemos elevado la seguridad del código sanitizando las entradas, eliminando el riesgo de SQL Injection. Sin embargo, **el sistema sigue siendo comprometido** por una política de contraseñas deficiente. La seguridad es una cadena y las credenciales por defecto son a menudo el eslabón más débil."
