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
