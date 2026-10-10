# Plan de Sustentación ING-TAR-5

## 1. Objetivo

Presentar de forma clara, profesional y argumentada la solución propuesta para el transporte intermunicipal de Urabá, demostrando que la arquitectura, la configuración, la gestión del repositorio y la estrategia de mitigación de riesgos fueron desarrolladas con criterio técnico.

## 2. Estructura de la sustentación

### A. Introducción (2 - 3 minutos)
- Contexto de Urabá: baja cobertura móvil, rutas críticas y alta necesidad de operación continua.
- Problema principal: venta de tiquetes y pagos offline en zonas con poca conectividad.
- Solución propuesta: arquitectura híbrida, transaccional, offline-first y basada en eventos.

### B. Arquitectura y decisiones de diseño (5 - 7 minutos)
- Cliente-servidor con nivel de eventos para telemetría.
- Redis + PostgreSQL para consistencia de asientos.
- SQLite local en inspección con patrón Outbox.
- Patrones Strategy y State.
- Uso de MQTT/Kafka para geolocalización.

### C. Gestión de configuración y trazabilidad (3 - 4 minutos)
- GitFlow con `main`, `develop` y `feature/OCI-001-pago-qr`.
- PR #2 mergeado con commit `7f6d88`.
- Tag `v1.1.0-QR`.
- CI/CD en GitHub Actions.
- Documentación y catalogación de ECS/EGC.

### D. Riesgos y métricas (3 - 4 minutos)
- Riesgos principales: señal, doble venta, sincronización, impresora, Redis.
- Métricas: tiempo de pago, tasa de doble reserva, sincronización offline, disponibilidad.
- Indicadores de calidad según ISO 25000.

### E. Demostración / evidencia (3 - 5 minutos)
- Mostrar la lógica del bloqueo con Redis.
- Mostrar la tabla `pagos_qr.sql`.
- Explicar el patrón Outbox y sincronización local.
- Explicar cómo se hace la impresión y fallback.

### F. Conclusión y cierre (1 - 2 minutos)
- La solución es viable para Urabá.
- Se mejora la continuidad operativa y la confiabilidad del sistema.
- El proyecto cumple con la práctica de ingeniería y la entrega del curso.

## 3. Guion recomendado de exposición

### Opening
> El principal desafío del sistema no es solo la venta, sino mantener la operación funcional donde la señal es inestable. Por eso diseñé una solución híbrida que combina consistencia en la central y operación local en campo.

### Desarrollo
> La venta de boletos usa Redis SETNX y PostgreSQL con SELECT FOR UPDATE y UNIQUE para evitar la doble venta. El pago QR offline se resuelve con SQLite local + Outbox, que sincroniza cuando vuelve la señal.

### Cierre
> El proyecto cumple la necesidad real de Urabá: un sistema operativo, seguro, disponible y medible, con trazabilidad técnica y control de configuración.

## 4. Material de apoyo recomendado
- README con resumen consolidado
- `docs/catalogo-ecs.md`
- `docs/oci-001.md`
- `docs/matriz-riesgos.md`
- `docs/dashboard-metricas.md`
- Diagrama de arquitectura (si se incluye en la presentación)

## 5. Criterios de evaluación a evidenciar

- Arquitectura coherente con el problema real del territorio.
- Uso de patrones y decisiones técnicas justificadas.
- Control de versión, PR y trazabilidad completos.
- Gestión de riesgos con medidas concretas.
- Calidad de documentación y sustentación.

## 6. Recomendaciones para la presentación

- Hablar con lenguaje técnico pero claro.
- Mostrar la solución como respuesta a una necesidad regional, no como software genérico.
- Enfatizar la diferencia entre arquitectura centralizada y arquitectura local-first.
- Presentar evidencias concretas: logs, tablas, PR, CI, métricas y decisiones de diseño.

## 7. Tiempo sugerido total

20 a 25 minutos, incluyendo preguntas.
