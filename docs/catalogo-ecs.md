# Catálogo de Elementos de Configuración

## 1. Elementos de Configuración de Software (ECS)

| ID | Nombre | Tipo | Descripción | Evidencia / Uso |
| --- | --- | --- | --- | --- |
| ECS-001 | app.py | Código de servicio / venta | API principal para la venta de boletos con bloqueo transaccional usando Redis Lock para evitar doble venta en el mismo asiento durante la ventana de reserva. | Central en la lógica de venta; usa Redis SETNX y PostgreSQL SELECT FOR UPDATE + UNIQUE. |
| ECS-002 | pagos_qr.sql | Script de base de datos | Tabla de pagos QR con soporte a pago offline-first de $18.000 bajo QR_NEQUI validado. Incluye restricciones y trazabilidad para sincronización posterior. | Base de datos operativa para pagos QR offline y validación de transacciones. |
| ECS-003 | redis.conf | Configuración de infraestructura | Configuración de Redis para cluster y control de bloqueo por asiento, TTL y manejo de concurrencia multi-sede. | Soporta reservas con lock temporal y coordinación entre puntos de venta. |
| ECS-004 | mqtt_broker.yml | Configuración de eventos | Broker MQTT/Kafka para telemetría de GPS y eventos en tiempo real; integra monitoreo del estado del bus y geocercas. | Permite mensajería de ubicación, seguimiento y sincronización de eventos. |
| ECS-005 | inspector_app/ | Aplicación móvil / local-first | Aplicación del inspector con SQLite local y patrón Outbox para sincronizar transacciones cuando regresa la señal. | Soporta operación offline en zonas con conectividad limitada como Turbo-Necoclí. |
| ECS-006 | QrPagoStrategy.py | Implementación de patrón Strategy | Estrategia para impresión y entrega de tiquetes dependiendo del tipo de impresora: 58mm / 80mm; fallback y canal alternativo por WhatsApp si falla la impresión. | Asegura continuidad operativa en entornos con hardware y conectividad variables. |
| ECS-007 | EncomiendaState.py | Implementación de patrón State | Manejo del ciclo de vida de encomiendas para evitar transiciones inválidas y garantizar consistencia del estado. | Regula estados como EnTransito, Entregado, Devolución y validaciones de flujo. |
| ECS-008 | wireframes.mockflow | Prototipo de UX / diseño | Wireframes del sistema con flujo de venta, taquilla, inspección, geocercas y recepción de encomiendas. | Sirve como referencia de producto para validación funcional y experiencia de usuario. |

## 2. Elementos de Gestión de Configuración (EGC)

| ID | Nombre | Tipo | Descripción | Evidencia / Uso |
| --- | --- | --- | --- | --- |
| EGC-001 | Dockerfile | Contenerización | Definición del entorno de ejecución para la aplicación y sus dependencias, facilitando despliegue consistente. | Permite replicabilidad del entorno en desarrollo, QA y producción. |
| EGC-002 | GitHub Actions CI/CD | Automatización de despliegue y validación | Pipeline de integración continua para validación, pruebas y despliegue automatizado del proyecto. | Asegura trazabilidad, calidad y control de cambios. |

## 3. Checklist de auditoría de configuración

1. [x] El repositorio está versionado con estructura clara y trazabilidad por cambios.
2. [x] Se usa GitFlow con rama main para producción y ramas de trabajo para cambios funcionales.
3. [x] La lógica de bloqueo de asientos usa Redis SETNX con TTL para evitar doble venta.
4. [x] La base de datos usa restricciones de unicidad y bloqueo transaccional para consistencia.
5. [x] El pago QR offline está validado con soporte a $18.000 y QR_NEQUI.
6. [x] La aplicación del inspector opera sin conectividad usando SQLite con patrón Outbox.
7. [x] La configuración de telemetría y GPS está centralizada mediante MQTT/Kafka.
8. [x] Los patrones de diseño clave (Strategy y State) están implementados y documentados.
9. [x] La configuración de despliegue y entorno está contenida y reproducible con Docker.
10. [x] El pipeline de CI/CD en GitHub Actions está habilitado, validado y asociado a la entrega.

## 4. Resumen

Este catálogo consolida los elementos de configuración críticos del proyecto Flotas Urabá Cootransuraba, con foco en: consistencia transaccional, operación offline, trazabilidad, automatización y despliegue seguro. La auditoría aplicada confirma que la solución está alineada con la estrategia de resiliencia operativa y control de calidad requerida para Urabá.
