# Importación Excel Maestro (Fase 3)

## Objetivo

Permitir la carga manual de una base longitudinal `BD_VISITAS_HS` en la herramienta web estática para ayudar a precargar datos previos en la pestaña de Seguimiento.

## Cómo cargar el Excel maestro

1. Ir a la pestaña **Seguimiento**.
2. En el bloque **Base longitudinal cargada**, pulsar **Cargar Excel maestro**.
3. Seleccionar un archivo `.xlsx`.
4. Confirmar estado:
   - `Sin base cargada`
   - `Base cargada: X registros / Y pacientes`
   - `Última carga: fecha/hora`

## Hoja requerida

El archivo debe contener la hoja:

- `BD_VISITAS_HS`

## Columnas mínimas críticas

Se valida como mínimo que existan:

- `fecha_visita`
- `tipo_visita`
- `nuhsa`
- `codigo_hs`
- `ihs4_actual`
- `hurley_enfermeria`
- `peso_kg`
- `tabaco_estado`
- `tratamiento_activo`

Si faltan columnas críticas, la carga se bloquea.

Si hay columnas adicionales, se muestra aviso y la carga continúa.

## Qué datos se precargan en Seguimiento

Al pulsar **Buscar última visita** (por `NUHSA` o `codigo_hs`):

- `sg_fecha_ultima_consulta` desde `fecha_visita` previa.
- `sg_tiempo_desde_ultima` (si hay fecha de visita SG).
- `sg_ihs4_previo` desde última visita clínica (`PV/SG`).
- `sg_hurley` orientativo.
- `sg_peso` previo.
- `sg_tabaco` previo.
- `sg_tratamiento_realizado` con tratamiento activo previo.
- `sg_codigo_hs` si existe.

## Qué datos NO se precargan

No se precargan campos de actividad actual, por seguridad clínica:

- IHS4 actual;
- EVA actuales;
- brotes actuales;
- PROMs actuales.

## Reapertura de Primera Visita desde la base cargada

Además de la precarga de Seguimiento, la base cargada permite reabrir la Primera Visita vigente del paciente seleccionado desde la barra lateral (botón **Abrir Primera Visita guardada**), restaurando los valores almacenados en su fila PV y manteniendo su fecha de visita original. Ver el detalle en el [manual de usuario](MANUAL_USUARIO_ENFERMERIA_HS.md).

### Versiones de Primera Visita y corrección append-only

Puede haber varias filas `tipo_visita = PV` del mismo paciente porque la Primera Visita se haya reabierto y corregido. Son **versiones** de la misma visita clínica, no visitas distintas.

- La versión vigente (canónica) es la fila PV con `fecha_exportacion` válida más reciente. `fecha_visita` no decide qué revisión es la vigente.
- Para corregir una PV, pegue la nueva fila **al final** de `BD_VISITAS_HS`. No elimine ni sobrescriba la fila anterior: se conserva como histórico y auditoría.
- Las revisiones anteriores siguen en el Excel, pero no cuentan como visitas clínicas en el resumen longitudinal, la tendencia IHS4 ni los contadores del dashboard.
- Compatibilidad con datos antiguos: si varias PV del paciente no tienen `fecha_exportacion` válida, la herramienta usa de forma determinista la última fila física del Excel y muestra un aviso no bloqueante al abrir la PV.
- Seguimiento (SG) y Cura Post-Qx (CX) no se versionan ni se deduplican: cada fila SG/CX sigue contando como su propia visita.

## Seguridad y persistencia

- Sin backend.
- Sin envío de datos a servicios externos.
- Sin telemetría/analytics añadidos.
- Datos cargados en memoria y `sessionStorage` de la pestaña.
- Sin `localStorage` para datos clínicos.
- Al cerrar la pestaña/sesión, la base se pierde.
- Usar solo archivos en entorno autorizado.

## Prueba recomendada

Archivo de prueba incluido:

- `templates/BD_VISITAS_HS_sintetico.xlsx`

