# AGENTS.md — Enfermería HS HUVRocío

## Rol del agente

Actúa como desarrollador frontend senior y asistente técnico-documental. Este repositorio contiene una herramienta clínica piloto para consulta de Enfermería en Hidradenitis Supurativa del Hospital Universitario Virgen del Rocío.

La prioridad es mantener la herramienta funcional, segura, sencilla y útil para uso real en consulta.

## Contexto funcional

La herramienta se usa en una consulta de Enfermería previa a Dermatología. El objetivo es:

1. Recoger información que Dermatología/Giovanna ya necesita o recoge habitualmente.
2. Registrar valor añadido de Enfermería.
3. Generar informe de texto para pegar en historia clínica.
4. Exportar una fila tabulada compatible con una base de datos longitudinal.
5. Preparar futura integración con PROMs remotos vía Microsoft Forms.

## Reglas clínicas de visualización

Los campos con asterisco `(*)` son datos alineados con Dermatología/Giovanna o útiles para consulta médica.

Los campos sin asterisco son datos añadidos por Enfermería para seguimiento, educación sanitaria, curas, adherencia, PROMs o explotación/publicación.

No eliminar PROMs ni escalas sin instrucción explícita.

No usar lenguaje de “derivación” como acción de Enfermería. Usar:

- “Necesidades a valorar por Dermatología”
- “Recomendaciones/necesidades a comentar con Dermatología”

## Reglas de seguridad

- No hay backend.
- No guardar datos clínicos identificables en `localStorage`.
- Usar `sessionStorage` y variables en memoria para datos cargados durante la sesión. La base cargada debe perderse al cerrar la pestaña/sesión.
- No enviar datos a servicios externos.
- No introducir analytics, telemetría ni llamadas externas innecesarias.
- Si se usa SheetJS, vendorizar localmente el archivo en `/vendor/xlsx.full.min.js`.

## Arquitectura deseada

Evolucionar progresivamente desde `index.html` único hacia:

```text
index.html
assets/css/styles.css
assets/js/config.js
assets/js/state.js
assets/js/utils.js
assets/js/ihs4.js
assets/js/forms-pv.js
assets/js/forms-sg.js
assets/js/forms-cx.js
assets/js/reports.js
assets/js/export.js
assets/js/import-excel.js
vendor/xlsx.full.min.js
docs/PLAN.md
docs/DICCIONARIO_VARIABLES.md
```

No hacer una refactorización masiva si pone en riesgo la herramienta. Dividir en fases pequeñas.

## Exportación

La exportación debe orientarse a una hoja única:

`BD_VISITAS_HS`

Todas las pestañas deben exportar la misma estructura de columnas.

Usar `tipo_visita`:

- `PV`
- `SG`
- `CX`

Dejar campos vacíos cuando no apliquen.

## Código anónimo

Formato obligatorio:

- `HS0001`
- `HS0002`
- `HS0003`

No usar hash, fecha ni sufijos.

El código debe poder introducirse fácilmente por el paciente en Microsoft Forms.

## Pruebas mínimas tras cada cambio

Antes de hacer commit:

1. Abrir herramienta en navegador.
2. Probar cambio de pestañas.
3. Probar Primera Visita.
4. Probar Seguimiento.
5. Probar Cura Post-Qx.
6. Probar Copiar Informe.
7. Probar Copiar Excel.
8. Revisar consola sin errores.
9. Confirmar que no quedan referencias rotas a campos eliminados.

## Estilo de commits

Usar mensajes claros:

- `fase 1 adapta campos clinicos y asteriscos`
- `fase 2 unifica exportacion longitudinal`
- `fase 3 modulariza estructura frontend`
- `fase 4 añade importacion excel temporal`
- `fase 5 añade codigo anonimo hs`

## Prioridades actuales tras entrada en piloto real

La herramienta está en uso piloto real en consulta de Enfermería HS del HUVR. El orden de prioridad actual es:

### 1. Incidencias de uso real (máxima prioridad)
- Cualquier bug, bloqueo o comportamiento inesperado reportado desde consulta se atiende antes que cualquier desarrollo nuevo.

### 2. Informe TXT para historia clínica (prioridad alta)
- Rediseñar informes PV, SG, CX como texto plano compatible con Iraya/Diraya.
- Sin HTML, sin negritas, sin maquetación web.
- Estructura clínica ordenada: motivo de consulta, exploración, IHS4, EVAs, zonas activas detalladas (nódulos/abscesos/fístulas por región), necesidades a valorar por Dermatología, registro añadido de Enfermería.
- No asumir que la historia clínica soporta negritas ni formato — usar solo texto plano a menos que se confirme lo contrario con el servicio de informática del HUVR.

### 3. QuickViews clínicos (prioridad media-alta)
- Vistas rápidas de indicadores del paciente activo sin salir de la pestaña actual.

### 4. Dashboard v0 (prioridad media)
- Vista de indicadores agregados del proyecto en pestaña dedicada.

### 5. Refactor modular (pospuesto)
- No iniciar refactor amplio mientras la herramienta está en uso real.
- Cualquier cambio debe hacerse sobre el `index.html` monolítico actual.

### Reglas de trabajo reforzadas
- **Cambios pequeños**: cada cambio debe ser atómico y limitado en alcance. No mezclar tareas.
- **Rama + PR**: todo cambio se hace en rama nueva desde `main`, con PR y revisión antes de merge. No tocar `main` directamente.
- **No localStorage**: los datos cargados van a `sessionStorage` y se pierden al cerrar la pestaña. No guardar datos clínicos identificables en `localStorage`.
- **No refactor amplio sin aprobación**: el refactor modular está explícitamente pospuesto. No iniciarlo sin aprobación de la responsable del proyecto.
