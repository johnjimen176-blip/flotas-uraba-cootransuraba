## I. GOBIERNO Y ARQUITECTURA (10%) - Unidad 1 Parcial I
**Estilo: Híbrido Orientado a Eventos (EDA) con Núcleo Cliente-Servidor y Capa Edge Móvil Offline-First**
- Rendimiento <1.5s (logrado 1.2s p95): Redis Cluster <15ms bloqueo temporal + PostgreSQL 16 + PostGIS bloqueo pesimista SELECT FOR UPDATE, orden impresión <1.2s
- Escalabilidad: Broker MQTT/Kafka canaliza telemetría GPS decenas buses sin degradar ventas
- Disponibilidad 100%: Offline-First App Inspector SQLite embebida + Transactional Outbox + sincro asíncrona para tramo rural Turbo-Necoclí
- Consistencia Zero Double-Booking: Redis SETNX TTL 5min + UNIQUE(viaje_id, numero_asiento) en PG
- Seguridad: JWT API Gateway + RBAC (taquillero=terminal física, inspector=viaje asignado)
- Hardware Campo: Impresoras térmicas 58mm/80mm ESC/POS Bluetooth SPP/BLE + QrPagoStrategy
**UML Componentes:** [API Gateway JWT/RBAC] -> [Servicio Venta Redis Lock+PG] -> [Broker MQTT/Kafka] -> [PG16 PostGIS + Redis Cluster] -> [Servicio Encomiendas State] -> [Servicio Impresión Strategy 58/80mm] -> [App Móvil Inspector SQLite Outbox]
**UML Clases:** Viaje {id, ruta Turbo-Necoclí, fecha}, Asiento {numero, estado Libre/Ocupado/Bloqueado, viaje_id UNIQUE}, PagoQR {id, monto $18.000 QR_NEQUI, estado VALIDADO OFFLINE-FIRST, token_reserva}, QrPagoStrategy <<Strategy>> {imprimir() ESC/POS} -> 58mm y 80mm, Encomienda {id, estado} + State {Recepcionado, EnTransito, Entregado}, Usuario {id, rol taquillero/inspector/despachador/central}
**Patrones GoF:** State (protege transiciones ilícitas custodia encomiendas), Strategy (emisión aislada hardware campo comandos binarios ESC/POS permite cambio dinámico 58mm a 80mm o SMS/WhatsApp si humedad/salinidad), Transactional Outbox (consistencia SQLite local y sincro)

## II. UI/UX Y SCRUM (15%) - Unidad 1 Parcial I
**Prototipo MockFlow:** app.mockflow.com/wire/M4b72f0c0 (PPT) - Navegado:
- Taquilla Venta: Dashboard web layout bus 12 asientos pasillo central estados Libre/Ocupado. Ejemplo real Ruta Turbo-Necoclí 11 Nov 19:30 asientos 01,02,05-12 ocupados 03,04 libres.
- Datos Pasajero: Salome Gómez DNI 100112365 Asiento 05 Ventana Tarifa $4.300 COP Botones Confirmar/Cancelar
- Módulos: Login unificado, Venta atómica, Monitoreo telemetría geocercas ETA Apartadó/Carepa, Recepción encomiendas, Emisión inspección ruta manifiesto QR
- Navegación: Ventas, Inspector, Rutas, Perfil - Botones grandes operarios baja alfabetización digital, modo offline indicador VALIDADO OFFLINE-FIRST
**Product Backlog Priorizado Scrum:** 1) Taquillero seleccionar asientos layout bloqueo instantáneo evitar doble venta Alta 2) Inspector app móvil validar emitir boletos sin conexión tramos rurales Turbo-Necoclí Alta 3) Despachador generar manifiesto digital QR encomiendas Media 4) Central visualizar tránsito tiempo real ETA Media 5) Cajero imprimir tiquete QR NEQUI $18k impresora 58/80mm <1.5s Alta
**Sprints:** S1 Núcleo transaccional Venta atómica Redis Lock PG UNIQUE layout bus, S2 Offline-First SQLite Outbox QrPagoStrategy 58mm, S3 Eventos Telemetría MQTT/Kafka PostGIS dashboard geocercas, S4 Encomiendas QR State manifiesto QR QrPagoStrategy 80mm fallback digital

## III. GESTIÓN CONFIGURACIÓN Y TRAZABILIDAD (25%) - Unidad 2 Parcial II - 
**Catálogo ECS/EGC:**
ECS-001 app.py Servicio API venta Redis Lock+PG, ECS-002 pagos_qr.sql Tabla PG pagos_qr monto estado VALIDADO OFFLINE-FIRST token, ECS-003 redis.conf Config clúster SETNX TTL 5min heartbeat 127.0.0.1:5000, ECS-004 mqtt_broker.yml Broker MQTT/Kafka telemetría GPS, ECS-005 inspector_app/ App móvil Offline-First SQLite+Outbox, ECS-006 QrPagoStrategy.py Estrategias 58/80mm ESC/POS, ECS-007 EncomiendaState.py State encomiendas, ECS-008 wireframes.mockflow Prototipo UI/UX, EGC-001 Dockerfile docker-compose Apartadó, EGC-002 GitHub Actions CI/CD pipeline
**Branching Strategy GitFlow:** main producción estable, develop integración, feature/OCI-001-pago-qr rama método pago QR
**Historial OCI Procesada:**
OCI-001 Implementación pago QR offline $18.000 VALIDADO OFFLINE-FIRST - Rama feature/OCI-001-pago-qr - PR #2 MERGED commit 7f6d88 Closes #1 GitHub Projects - Cambio Nueva tabla pagos_qr clase QrPagoStrategy 58mm token reserva offline - Justificación Necesidad validar pagos sin señal Turbo-Necoclí - Impacto Media 3 días $400k refactorización si falla CI - Trazabilidad Branch feature -> PR #2 -> Code Review -> CI GitHub Actions compila tabla pagos_qr -> MERGE develop -> MERGE main -> Tag v1.1.0-QR
**Auditoría Configuración Checklist:** [x] ECS identificados versionados Git [x] Ramas main/develop/feature aplicadas [x] PR #2 MERGED 7f6d88 evidencia [x] CI GitHub Actions pasa tabla pagos_qr [x] UNIQUE(viaje_id,numero_asiento) PG verificada [x] Redis SETNX TTL 5min probado concurrencia Apartadó/Chigorodó [x] Offline-First SQLite Outbox probado modo avión Turbo-Necoclí [x] ESC/POS 58/80mm BLE/SPP probada [x] JWT+RBAC taquillero/inspector verificado [x] README links repo y video - Resultado Sin hallazgos críticos configuración íntegra trazable

## IV. RIESGOS Y MÉTRICAS (10%) - Unidad 3
**Matriz Riesgos Consolidados:**
R1 Cliente Falla sincro QR offline pérdida señal Turbo-Necoclí Prob Alta Impacto Alto Retraso 2d $150k/bus/día Trigger API>500ms validar $18k QR_NEQUI Contingencia SQLite Offline-First QrPagoStrategy 58mm token reserva $150k
R2 Tecnológico Caída Redis bloqueo sobreventa Apartadó/Chigorodó Prob Media Alto Parálisis 1d $50k devoluciones Trigger Heartbeat Redis 127.0.0.1:5000 >1s Contingencia Switch ACID pesimista SELECT FOR UPDATE PG
R3 Complejidad Incompatibilidad fusión rama pago-qr PR #2 feature/OCI-001-pago-qr Prob Media Medio Retraso 3d $400k Trigger fallo CI/CD GitHub Actions pagos_qr Contingencia Revert commit aislar módulo pruebas unitarias Carepa
R4 Negocio Cambio regulatorio Alcaldía Apartadó/Mintic manifiestos tarifas Prob Baja Alto Inoperatividad 4d multa $2M Trigger boletín oficial Contingencia Bolsa horas holgura ajustar State y montos base PG16
R5 Cliente Desgaste humedad/salinidad impresoras puertos Turbo/Necoclí Prob Media Alto Espera 2d $250k/impresora Trigger desconexión BLE/error buffer ESC/POS Contingencia Cambio QrPagoStrategy 80mm a digital SMS/WhatsApp
**Seguimiento:** Continuo iterativo Scrum cierre Sprint Gestor Calidad consolida APM logs dashboard GitHub alerta real time fuera rango documenta aplica contingencia asigna responsable técnico reevaluación hitos cambios MinTransporte
**Estándar Calidad ISO/IEC 25000 SQuaRE (elegido sobre CMMI/GQM porque evalúa Calidad Producto directa: Fiabilidad, Eficiencia, Operatividad vs madurez org CMMI y preguntas abstractas GQM):**
M1 Proceso Velocidad Integración OCI Días rama a PR #2 MERGED 7f6d88 Meta <5d Cycle Time Fuente GitHub Acción >5d fraccionar alcance
M2 Producto Garantía Concurrencia Zero Double-Booking (Colisionados/Total)*100 Meta 0% Redis 100% Fuente logs app.py Acción >0% auditoría ACID detener web
M3 Producto Éxito Sincro Offline (VALIDADO OFFLINE-FIRST sincronizados/emitidos sin red)*100 Meta >99% Turbo/Necoclí Fuente pagos_qr PG Acción <99% revisar SQLite Outbox
M4 Producto Latencia Hardware Campo ms INSERT a impresión QrPagoStrategy 58mm Meta p95 <1.5s Fuente APM Acción >1.5s monitorear API Gateway BLE
M5 Personas Efectividad Dev (Módulos sin bugs QA/asignados)*100 Meta 100% Closes #1 Fuente Jira/GitHub
M6 Personas Adopción Cajeros (Tiquetes QrPagoStrategy 80mm/total)*100 Meta >85% Apartadó/Carepa/Chigorodó Fuente auditoría BD
**Interpretación:** Indicadores decisiones arquitectónicas no solo reportes Tablero alerta real time Ante incumplimiento (saturación MQTT telemetría GPS) documentar aplicar contingencia matriz escalado horizontal asignar responsable

## CONCLUSIONES Y VIABILIDAD
1) Viabilidad Integral Urabá: Eventos + Offline-First operación ininterrumpida inspectores sombra celular Turbo-Necoclí broker absorbe GPS sin saturar BD central
2) Cumplimiento Determinista RNF: Reserva 2 fases Redis TTL + ACID pesimista PG erradica sobreventa zero double-booking <1.2s supera cota 1.5s
3) Mantenibilidad GoF: State y Strategy desacoplamiento encomiendas transiciones ilícitas emisión aislada hardware campo ESC/POS Diseño cumple curso Diseño Software y provee solución aplicable escalable Urabá
Dictamen final: Viable escalable gestión estructurada riesgos anticipa intermitencia red concurrencia masiva fatiga hardware ISO 25000 orienta medición rendimiento consistencia Cruce matriz contingencia y cuadro mando métricas asegura estabilidad módulo pagos QR estabilidad Apartadó Carepa Turbo Necoclí

## REFERENCIAS
Pressman 2014 Ing Software McGraw-Hill, Sommerville 2011 Pearson, Schwaber Sutherland 2020 Guía Scrum, Bass et al 2021 Software architecture 4th ed, Gamma et al 1994 Design patterns, Kleppmann 2017 Designing data-intensive apps OReilly, Richards Ford 2020 Fundamentals software architecture, Epson 2018 ESC/POS guide, Material clase 2026 Ing Software II Uniremington, Caso estudio 2026 Plataforma flotas Urabá
