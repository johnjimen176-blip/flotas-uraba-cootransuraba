# Plan de Configuración y Repositorio

## 1. Objetivo

Definir la estrategia de control de configuración, trazabilidad y repositorio del proyecto para garantizar la integridad de cambios, despliegue y mantenimiento del sistema.

## 2. Estructura de ramas

| Rama | Uso |
| --- | --- |
| `main` | Producción |
| `develop` | Integración |
| `feature/OCI-001-pago-qr` | Cambio funcional principal |

## 3. Flujo de trabajo

1. Se desarrolla el cambio en una rama de feature.
2. Se valida el comportamiento local y en CI.
3. Se crea un Pull Request a `develop`.
4. Se ejecuta Code Review.
5. Si todo pasa, el PR se hace merge.
6. Se etiqueta la versión estable con Git tags.

## 4. Trazabilidad de la OCI

| Elemento | Valor |
| --- | --- |
| Rama | `feature/OCI-001-pago-qr` |
| PR | #2 |
| Estado | MERGED |
| Commit de cierre | `7f6d88` |
| Tag | `v1.1.0-QR` |
| CI | Aprobado |

## 5. Reglas de configuración

- Todo cambio funcional debe tener una rama específica.
- Todo PR debe tener revisión y validación.
- Todo merge debe registrar la causa y el alcance del cambio.
- Todo release debe tener tag asociado.
- Todo cambio crítico debe ser documentado con una OCI.

## 6. Auditoría del repositorio

| Check | Estado |
| --- | --- |
| Versionado en Git | ✓ |
| Rama principal protegida | ✓ |
| Feature branch asociada | ✓ |
| PR con revisión | ✓ |
| CI/CD ejecutado | ✓ |
| Tag de release | ✓ |
| Checklist de auditoría | ✓ |

## 7. Conclusión

El control de configuración es una parte central de la solución, porque la operación real del sistema depende de la trazabilidad, validación y continuidad del cambio en producción.
