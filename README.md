# Flotas Urabá Cootransuraba - Informe Consolidado Final ING-TAR-5 25%

**Estudiante:** John Jimenez Moreno - Uniremington Apartadó - Ing. Software II 

Hice este proyecto pensando en lo que vivo todos los días entre Chigorodó, Apartadó y Turbo. El problema real es que entre Turbo y Necoclí se va la señal, en taquilla a veces venden el mismo puesto dos veces y los GPS mandan datos cada 5 segundos y saturan todo.

## I. GOBIERNO Y ARQUITECTURA (10%) - Unidad 1 Parcial I

Me fui por un estilo híbrido, no todo microservicios porque en Urabá no aguanta. Un núcleo cliente-servidor normal, con una parte de eventos para la telemetría y una capa móvil offline-first para el inspector.

Para cumplir lo de <1.5s usé Redis para bloquear el asiento 5 minutos con SETNX y en PostgreSQL le puse un SELECT FOR UPDATE más un UNIQUE de viaje_id y numero_asiento. Con eso no hay doble venta. Si se cae Redis el sistema sigue con Postgres. En mis pruebas me dio 1.2s p95.

Lo más crítico para mí fue lo offline. La app del inspector guarda en SQLite local y con el patrón Outbox sincroniza cuando vuelve la señal. Así puede vender un tiquete QR de $18.000 en Necoclí sin datos y queda como VALIDADO OFFLINE-FIRST.

Para las impresoras usé el patrón Strategy porque en Turbo la humedad daña el Bluetooth. Si falla la de 58mm cambia a 80mm y si no, manda el tiquete por WhatsApp. Eso lo probé con una impresora que me prestaron en Carepa.

Mis componentes son: API Gateway con JWT y RBAC, Servicio de Venta con Redis y PG, Broker MQTT/Kafka para los GPS, PG16 con PostGIS y Redis Cluster, Servicio de Encomiendas con State, Servicio de Impresión con Strategy y la App móvil.

Mis clases: Viaje, Asiento, PagoQR con estado offline, QrPagoStrategy, Encomienda y Usuario. Patrones que usé: State para que una encomienda no se devuelva de Entregado a EnTransito y Strategy para las impresoras y el Outbox para la sincro.

## II. UI/UX Y SCRUM (15%) - Unidad 1 Parcial I

El prototipo lo hice en MockFlow. Hice un bus de 12 puestos bien simple. Puse el caso de Salomé Gómez DNI 100112365 asiento 05 ventana $4.300 COP de Apartadó a Turbo. Lo hice con botones grandes porque los taquilleros me dijeron que no quieren letras chiquitas.

Módulos que prototipé: login unificado, taquilla con el mapa del bus, monitoreo con geocercas y ETA, recepción de encomiendas con QR y el módulo de inspección.

Mi Product Backlog lo prioricé así: 1) Que el taquillero bloquee el puesto al instante para no vender doble, 2) Que el inspector pueda trabajar sin internet en Turbo-Necoclí, 3) Que el despachador genere manifiesto con QR, 4) Que central vea en tiempo real donde va cada bus, 5) Que el cajero imprima QR NEQUI $18k en menos de 1.5s.

Lo dividí en Sprints: S1 hice lo transaccional de la venta, S2 todo lo offline-first, S3 los eventos de telemetría con MQTT y S4 las encomiendas con QR.

## III. GESTIÓN DE CONFIGURACIÓN Y TRAZABILIDAD (25%) - Unidad 2 Parcial II


Mis ECS son: app.py que es la API de venta, pagos_qr.sql donde creé la tabla de pagos, redis.conf con la config del SETNX 5min, mqtt_broker.yml para los GPS, la carpeta inspector_app con SQLite, QrPagoStrategy.py para imprimir en 58/80mm, EncomiendaState.py y los wireframes del MockFlow. Y como EGC el Dockerfile y el GitHub Actions.

Usé GitFlow: main para producción, develop para integración y para esta entrega creé la rama feature/OCI-001-pago-qr.

Mi OCI-001 fue implementar el pago QR offline de $18.000. La trabajé en esa rama, hice el PR #2 y ya está MERGED con commit 7f6d88 que cierra el issue #1. Le puse el tag v1.1.0-QR y el CI pasó ok.

Mi checklist de auditoría: todo versionado, PR mergeado, CI ok, UNIQUE probada, Redis lock probado vendiendo el mismo puesto desde Apartadó y Chigorodó al tiempo y no dejó, offline probado en modo avión y la impresora 58mm probada.

## IV. RIESGOS Y MÉTRICAS (10%) - Unidad 3

Riesgos que vi yo en Urabá: R1 que se pierda la señal en Turbo-Necoclí y no se pueda cobrar el QR, probabilidad alta, lo arreglo con SQLite local. R2 que se caiga Redis y vendan doble en Apartadó, probabilidad media alta, paso a SELECT FOR UPDATE en PG. R3 que el PR #2 no pase el CI, media, hago revert y pruebo en Carepa. R4 que la alcaldía o MinTIC cambie lo de manifiestos o tarifas, baja pero impacto alto, dejé bolsa de horas. R5 que las impresoras se dañen por humedad y salinidad en Turbo, media, cambio a SMS/WhatsApp.

Para calidad escogí ISO 25000 porque es más de producto y me sirve para medir rendimiento y fiabilidad, CMMI es muy de empresa grande y GQM es muy abstracto.

Mis métricas: M1 que una OCI no dure más de 5 días de rama a merge, M2 0% doble reserva revisando logs de app.py, M3 más de 99% de sincronización offline en Turbo/Necoclí revisando tabla pagos_qr, M4 latencia p95 menor a 1.5s de insert a impresión, M5 efectividad del dev 100% con Closes #1 y M6 adopción mayor a 85% en taquillas de Apartadó, Carepa y Chigorodó.

Si algo se sale de rango lo documento y aplico la contingencia de la matriz y aviso en el dashboard.

## Conclusión

Al final sí se puede hacer un sistema que aguante Urabá. Con eventos más offline-first se puede trabajar sin señal y sin vender doble. A mí me dio 1.2s que es mejor que 1.5s que pedían. Es viable y se puede escalar a otras rutas de Cootransuraba.

## Referencias

Pressman, Sommerville, Guía Scrum de Schwaber, Bass de arquitectura, Gamma de patrones, Kleppmann de datos, guía ESC/POS de Epson y lo que vimos en clase con la profe Marta.
