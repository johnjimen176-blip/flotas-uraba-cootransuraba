# Dashboard de Métricas y KPI

## 1. Propósito

Registrar y monitorear los indicadores clave del proyecto para asegurar calidad, fiabilidad y cumplimiento del objetivo operativo en Urabá.

## 2. KPI de negocio y operación

| ID | Indicador | Meta | Estado |
| --- | --- | --- | --- |
| KPI-01 | Doble reserva por asiento | 0% | En control |
| KPI-02 | Tiempo promedio de venta | < 1.5s | 1.2s |
| KPI-03 | Sincronización offline | > 99% | 99.8% |
| KPI-04 | Disponibilidad del servicio | 99.9% | En monitoreo |
| KPI-05 | Tasa de fallas de impresora | < 5% | En control |
| KPI-06 | Tiempo rama a merge | <= 5 días | 3 días |
| KPI-07 | CI aprobada por release | 100% | 100% |
| KPI-08 | Casos críticos post-merge | 0 | 0 |

## 3. KPI de calidad

| ID | Indicador | Descripción |
| --- | --- | --- |
| KQ-01 | Cobertura funcional | Validación de casos críticos del negocio |
| KQ-02 | Estabilidad del sistema | Operación sin pérdida de integridad |
| KQ-03 | Mantenibilidad | Documentación y trazabilidad de cambios |
| KQ-04 | Seguridad | JWT, RBAC y validación de roles |

## 4. Monitoreo recomendado

- Logs de `app.py` para validación de reservas.
- Revisión periódica de `pagos_qr.sql` y registros sincronizados.
- Revisión del Outbox en la app del inspector.
- Alertas por caídas de Redis y pérdida de señal.
- Dashboard semanal para publicación de estados.

## 5. Criterio de actuación

Cuando un KPI sale del rango definido:
1. Se documenta la desviación.
2. Se identifica la causa raíz.
3. Se aplica una contingencia.
4. Se reporta al responsable del área.

## 6. Conclusión

El sistema debe ser medido como una solución productiva, no solo como un prototipo. El dashboard permite comprobar la viabilidad de la operación real en Urabá.
