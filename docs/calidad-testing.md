# Calidad y Testing

## 1. Objetivo

Definir los criterios de calidad, pruebas y validación del sistema para garantizar confiabilidad, consistencia y continuidad operativa.

## 2. Enfoque de Calidad

Se adoptó la norma **ISO 25000** por su enfoque en producto y calidad de software en operación real.

### Criterios principales
- Funcionalidad
- Fiabilidad
- Eficiencia
- Mantenibilidad
- Portabilidad
- Seguridad

## 3. Criterios de validación

| Criterio | Descripción | Indicador |
| --- | --- | --- |
| Consistencia de venta | No puede venderse un mismo asiento dos veces | 0% doble reserva |
| Disponibilidad en entorno rural | El sistema debe operar sin señal | > 99% sincronización offline |
| Tiempo de respuesta | Venta y validación del pago deben cumplir umbral | < 1.5 s |
| Seguridad | JWT y RBAC | Acceso restringido por rol |
| Trazabilidad | Todo cambio documentado y revisado | PR + CI + tag |

## 4. Tipo de pruebas

### 4.1 Pruebas unitarias
- Validación de estados de encomienda.
- Validación de estrategia de impresión.
- Validación de reglas de negocio del asiento.

### 4.2 Pruebas de integración
- Pago QR con base de datos y Redis.
- Sincronización Outbox con aplicación local.
- Mensajería MQTT.

### 4.3 Pruebas de campo
- Simulación de pérdida de señal en Turbo-Necoclí.
- Venta con dispositivo offline.
- Validación de fallback por impresora y WhatsApp.

## 5. Evidencias de calidad

| Evidencia | Descripción |
| --- | --- |
| `pagos_qr.sql` | Reglas de negocio y validación SQL |
| `app.py` | Lógica de venta y control transaccional |
| `QrPagoStrategy.py` | Validación de estrategia de impresión |
| `EncomiendaState.py` | Manejo de estados con transiciones válidas |
| GitHub Actions | Verificación automatizada |

## 6. Indicadores clave

| Indicador | Meta |
| --- | --- |
| Tiempo de reserva | < 1.5 s |
| Tasa de doble reserva | 0% |
| Sincronización offline | > 99% |
| Tasa de errores críticos post-merge | 0 |
| Tiempo de OCI desde rama a merge | <= 5 días |

## 7. Conclusión

La calidad del sistema no se mide solo por su código, sino por su capacidad de operar en condiciones reales del territorio. En ese sentido, las pruebas y métricas fueron orientadas a disponibilidad, consistencia y trazabilidad.
