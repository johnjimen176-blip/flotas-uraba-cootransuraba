# Documento de Arquitectura del Sistema

## 1. Visión General

El sistema para Flotas Urabá Cootransuraba está diseñado para operar en un entorno con conectividad intermitente, alta movilidad y necesidad de confiabilidad en la venta de boletos, pagos y encomiendas.

## 2. Objetivos de Arquitectura

- Mantener disponibilidad operativa en rutas con señal limitada.
- Evitar la doble venta de asientos.
- Soportar pagos QR offline.
- Gestionar la telemetría en tiempo real.
- Asegurar trazabilidad de transacciones y revisión de auditoría.

## 3. Principios de Diseño

- Offline-first donde hay baja cobertura.
- Consistencia transaccional en la venta.
- Separación funcional por módulos.
- Uso de eventos para telemetría y sincronización.
- Arquitectura híbrida: central + local + cola asíncrona.

## 4. Componentes principales

### 4.1 API Gateway
- Gestiona autenticación JWT.
- Aplica RBAC por rol de taquillero, inspector y administrador.
- Actúa como punto de entrada para usuarios y servicios.

### 4.2 Servicio de Venta
- Ejecuta la lógica de bloqueo de asientos.
- Implementa validaciones con Redis y PostgreSQL.
- Persiste historial del movimiento del boleto.

### 4.3 Base de Datos PostgreSQL 16
- Almacena viajes, asientos, pagos, usuarios y encomiendas.
- Usa PostGIS para georreferenciación.
- Mantiene integridad con constraints y transacciones.

### 4.4 Redis
- Bloqueo temporal de asiento con SETNX.
- Coorina la reserva durante 5 minutos.
- Se utiliza además para control de sesiones y cache.

### 4.5 Broker MQTT/Kafka
- Transporta eventos de ubicación GPS y telemetría.
- Permite monitoreo en tiempo real de buses y paradas.
- Soporta análisis y alertas.

### 4.6 Aplicación del Inspector (offline-first)
- Guarda transacciones localmente en SQLite.
- Usa Outbox para sincronizar cuando regresa la señal.
- Permite ventas offline de QR y manejo de encomiendas.

### 4.7 Servicio de Encomiendas
- Implementa manejo de estados con patrón State.
- Evita transiciones inconsistentes.
- Registra el ciclo de vida de cada paquete.

### 4.8 Impresiones y pagos
- QrPagoStrategy define la estrategia de impresión según dispositivo.
- Soporta fallback a 80mm o WhatsApp.

## 5. Patrón de flujo de operación

### Venta de boleto
1. El usuario valida el viaje.
2. El servicio intenta bloquear el asiento en Redis.
3. Si la reserva es válida, se ejecuta SELECT FOR UPDATE.
4. Se persiste la operación con unique constraint.
5. Se confirma la venta o se rechaza por conflicto.

### Pago QR offline
1. El inspector o taquillero genera un QR local.
2. La transacción queda en SQLite y en el outbox.
3. Cuando vuelve la señal, se sincroniza al servidor.
4. El backend valida y confirma el pago.

## 6. Consideraciones de arquitectura

- El sistema aplica una política híbrida: centralizado para control y distribuido para resiliencia.
- Las decisiones se orientan a operación real en zonas rurales, no a una solución exclusivamente centralizada.
- La consistencia se prioriza mediante transacciones y bloqueo de recursos.

## 7. Diagrama de componentes (texto)

```text
[Usuarios / Taquilleros / Inspectores]
                 |
                 v
       [API Gateway + JWT + RBAC]
                 |
      +----------+------------+
      |                         |
      v                         v
[Servicio Venta]          [Servicio Encomiendas]
      |                         |
      +----+--------------------+
           |
           v
  [PostgreSQL 16 + PostGIS]
           |
           v
          [Redis]
           |
           v
 [MQTT/Kafka / GPS + Telemetría]
           |
           v
   [Inspector App SQLite + Outbox]
```

## 8. Conclusión

La arquitectura propuesta resuelve el problema práctico de Urabá, equilibrando consistencia, resiliencia y operatividad en condiciones reales de conectividad limitada.
