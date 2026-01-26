# 🏗️ Ejemplo de Modelado de Amenazas: Proceso de Compra Online

Este documento presenta un ejercicio de modelado de amenazas aplicado a una funcionalidad crítica de negocio, utilizando las metodologías **STRIDE** y **DREAD**.

---

## 1. Escenario: Sistema de Checkout
**Descripción:** Un usuario añade productos al carrito y procede al pago. El sistema envía los datos al servidor para procesar la transacción.

### Flujo de Datos (Simplificado):
1.  **Frontend:** Usuario pulsa "Comprar".
2.  **API Gateway:** Recibe petición POST con `item_id`, `quantity` y `total_price`.
3.  **Lógica Interna:** Verifica stock y procesa pago.
4.  **Base de Datos:** Actualiza pedido y stock.

---

## 2. Identificación de Amenazas (STRIDE)

| Categoría (STRIDE) | Amenaza Identificada | Descripción |
| :--- | :--- | :--- |
| **S**poofing | Suplantación de identidad | Un usuario intercepta la sesión de otro para realizar compras a su nombre. |
| **T**ampering | **Manipulación de Precios** | El usuario modifica el parámetro `total_price` en la petición HTTP para pagar 1€ por un producto de 1000€. |
| **R**epudiation | Repudio de Transacción | Un usuario afirma que no realizó la compra porque el sistema no guarda logs firmados de la operación. |
| **I**nformation Disclosure | Fuga de Datos de Pago | El sistema muestra los últimos 4 dígitos y el CVV en el historial por un error de diseño. |
| **D**enial of Service | Agotamiento de Stock | Un atacante añade 10.000 unidades al carrito sin pagar, bloqueando el stock para otros usuarios legítimos. |
| **E**levation of Privilege | Bypass de Admin | Un usuario cambia su `role_id` en la cookie para acceder al panel de devoluciones y aprobarse reembolsos. |

---

## 3. Evaluación de Riesgos (DREAD)
*Puntuación de 1 a 10 (donde 10 es el máximo riesgo)*

### Ficha de la Amenaza: Manipulación de Precios (Tampering)

| Categoría | Puntuación | Justificación |
| :--- | :--- | :--- |
| **D**amage Potential | 10 | Impacto financiero directo y crítico para el negocio. |
| **R**eproduced | 10 | Muy fácil de replicar una vez descubierto el parámetro. |
| **E**xploitability | 9 | Solo requiere una herramienta básica como Burp Suite o F12. |
| **A**ffected Users | 10 | Cualquier usuario de la plataforma puede intentar este ataque. |
| **D**iscoverability | 8 | Es una de las primeras pruebas que hace un auditor (o un atacante). |
| **TOTAL** | **47 / 50** | **RIESGO CRÍTICO** |

---

## 4. Estrategias de Mitigación

1.  **Validación en Servidor (Prioritaria):** NUNCA confiar en el precio enviado por el cliente. El servidor debe buscar el precio real en la Base de Datos basándose en el `item_id`.
2.  **Firma de Parámetros:** Si el precio debe viajar, usar un [HMAC](../FAQ_CONCEPTOS.md#8-hmac-hash-based-message-authentication-code) para asegurar que no ha sido alterado.
3.  **Logs de Auditoría:** Registrar cualquier discrepancia entre el precio enviado y el precio real para detectar intentos de fraude.
