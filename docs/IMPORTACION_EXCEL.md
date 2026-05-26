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

