# Matriz de Riesgos y Mitigación

## 1. Objetivo

Documentar los riesgos del sistema de transporte y venta de boletos para Urabá, con énfasis en conectividad, consistencia de datos, disponibilidad y operación en entorno rural.

## 2. Identificación de Riesgos

| ID | Riesgo | Descripción | Probabilidad | Impacto | Severidad | Mitigación | Responsable |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R1 | Pérdida de señal en Turbo-Necoclí | La red móvil cae durante la venta o sincronización del pago QR. | Alta | Crítico | 5/5 | SQLite local + Outbox + sincronización diferida + validación offline. | Sistema / Inspector |
| R2 | Doble venta del mismo asiento | Dos taquillas simultáneas venden el mismo puesto. | Media | Crítico | 5/5 | Redis SETNX con TTL + SELECT FOR UPDATE + UNIQUE (viaje_id, numero_asiento). | API Venta |
| R3 | Caída de Redis | El servicio de bloqueo de asientos queda indisponible. | Media | Alto | 4/5 | Redundancia de Redis Cluster + cache local + fallback de validación en base de datos. | Infraestructura |
| R4 | Fallo de impresora térmica | La impresora Bluetooth 58mm falla por humedad o defecto físico. | Alta | Medio | 3/5 | Strategy Pattern con fallback 80mm y WhatsApp como canal alternativo. | Caja / Venta |
| R5 | Datos inconsistentes al sincronizar | Pago generado offline llega duplicado o con estado incorrecto. | Media | Alto | 4/5 | Outbox + idempotencia + validación de estados + logs de auditoría. | Integración |
| R6 | Saturación o backlog MQTT/Kafka | El flujo de GPS y telemetría genera congestión de eventos. | Media | Medio | 3/5 | Broker con cola, particiones, priorización y monitoreo por zona. | IoT / Telemetría |
| R7 | Uso inadecuado del sistema por personal | Operadores usan la app sin seguir flujo de validación. | Media | Medio | 3/5 | RBAC, UI clara, validación de flujos y capacitación en campo. | Administración |
| R8 | Falla de disponibilidad del servicio central | La API central cae por mantenimiento o corte total. | Baja | Crítico | 4/5 | Modo offline local, sincronización diferida, cola de transacciones. | Infraestructura |

## 3. Matriz de Riesgos (P x I)

| Probabilidad / Impacto | 1 | 2 | 3 | 4 | 5 |
| --- | --- | --- | --- | --- | --- |
| **Alta (5)** | - | - | - | R3 | R1, R2 |
| **Media (4)** | - | - | - | R5, R8 | - |
| **Baja (3)** | - | - | R4, R6, R7 | - | - |

### Riesgos de mayor prioridad

1. R1: pérdida de señal en Turbo-Necoclí
2. R2: doble venta del mismo asiento
3. R5: inconsistencias al sincronizar
4. R3: caída de Redis
5. R8: indisponibilidad del servicio central

## 4. Estrategias de Control

- Revisión de riesgos antes de cada release
- Validación de integridad transaccional en cada sprint
- Monitoreo de conectividad y sincronización
- Backups y restauración del estado local
- Auditoría de logs y dashboard de métricas

## 5. Indicadores y seguimiento

| Indicador | Meta | Fuente |
| --- | --- | --- |
| Doble venta por asiento | 0% | app.py logs + PostgreSQL |
| Transacciones offline sincronizadas | > 99% | pagos_qr + Outbox |
| Tiempo promedio de reserva | < 1.5 s | métricas de API |
| Tasa de fallas de impresora | < 5% | logs de impresión |
| Disponibilidad del servicio | 99.9% | monitoreo del sistema |

## 6. Conclusión

La arquitectura híbrida, offline-first y transaccional del proyecto permite mitigar los riesgos principales del entorno de Urabá. La prioridad estratégica es garantizar continuidad operativa sin señal y consistencia de ventas durante la sincronización.
