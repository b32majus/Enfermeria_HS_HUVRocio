# Work Order — Reapertura de Primera Visita + IHS4=0 + auditoría

Status: EXECUTION_READY
Date: 2026-09-24
Repository: b32majus/Enfermeria_HS_HUVRocio

## Context

La aplicación está en piloto asistencial real en la consulta de Enfermería
de Hidradenitis Supurativa del Hospital Universitario Virgen del Rocío.

Se ha comunicado una incidencia de uso real:

una enfermera puede comenzar una Primera Visita (PV), exportar la fila al Excel
longitudinal y necesitar posteriormente volver a abrir esa Primera Visita para
consultarla, continuar completándola o modificarla.

La aplicación actual selecciona al paciente y permite usar Seguimiento y Cura,
pero la precarga clínica existente fue diseñada fundamentalmente para Seguimiento.
No existe una recuperación completa y explícita de la PV almacenada.

No se necesita clasificar una PV como borrador, incompleta o completa.

## Autoridad funcional

Al seleccionar un paciente existente por el identificador admitido actualmente
por la aplicación (NUHSA/AN según la UI actual) o por codigo_hs:

- mantener la selección normal del paciente;
- mantener disponibles Seguimiento y Cura Post-Qx;
- si existe una fila PV del paciente, ofrecer de forma clara la posibilidad de
  abrir esa Primera Visita;
- abrir la PV debe recuperar los datos de esa fila que realmente estén almacenados;
- la PV debe poder consultarse y también continuar/modificarse.

No inventar información que el Excel no almacena.

## Restricciones no negociables

NO cambiar el esquema del Excel real.

NO añadir columnas para:
- estado borrador/completa/incompleta;
- respuestas individuales de DLQI;
- respuestas individuales de HADS;
- respuestas individuales de HSQoL-24;
- desglose regional N/A/F no almacenado actualmente.

NO migrar datos existentes.

NO introducir backend.

NO introducir localStorage para datos clínicos.

NO introducir telemetría, analytics ni llamadas externas.

NO hacer refactor modular/amplio.

Mantener el index.html monolítico salvo que una modificación mínima en otro
archivo existente sea necesaria.

Preservar SG y CX.

No alterar código HS ni identidad del paciente salvo que sea imprescindible para
corregir un bug directamente relacionado y quede demostrado.

## Unidad 1 — Reabrir Primera Visita existente

### Comportamiento requerido

Cuando el paciente seleccionado tenga una PV existente:

1. Debe existir una acción explícita y comprensible para abrir la Primera Visita.
2. Debe cargarse la fila PV correspondiente en el formulario PV.
3. Deben restaurarse todos los campos cuyo valor esté realmente disponible en Excel.
4. La fecha de la PV existente debe mantenerse; no convertirla silenciosamente
   en una visita nueva con fecha actual.
5. Debe mantenerse el NUHSA/identificador y codigo_hs correctos.
6. La apertura de PV no debe interferir con la posibilidad de iniciar SG o CX.
7. Abrir una PV no debe crear un nuevo paciente ni reservar un nuevo codigo_hs.

### PROMs y escalas

Para DLQI, HADS y HSQoL-24:

- recuperar el total almacenado;
- mostrar correctamente ese resultado histórico;
- NO reconstruir respuestas individuales que nunca fueron almacenadas;
- NO inventar selecciones de ítems.

Para IHS4:

- recuperar score y componentes/totales que sí existan en la fila;
- recuperar zonas activas almacenadas;
- NO inventar distribución regional de lesiones no almacenada.

Cuando algún detalle del formulario no pueda reconstruirse porque nunca fue
persistido, la UI no debe presentar respuestas ficticias.

Puede mostrarse de forma discreta que el detalle no está almacenado si es
necesario para evitar ambigüedad.

### Varias filas PV

La aplicación no debe elegir de forma opaca entre varias PV si aparecen por
datos históricos anómalos.

Revisar primero la semántica y ordenación existentes.

Si puede resolverse determinísticamente y de forma clínicamente segura con la
autoridad actual, hacerlo y documentarlo.

Si existe una ambigüedad material que no pueda resolverse sin inventar política
de producto, STOP para esa decisión y reportarla.

### Reexportación de una PV abierta

La aplicación no escribe directamente en Excel: genera/copia una fila.

Evitar que el flujo induzca a pensar que una PV reabierta y modificada debe
añadirse como una segunda PV histórica.

Si se reexporta una PV cargada desde la base, proporcionar una indicación clara
de que esa fila corresponde a una actualización/reemplazo de la PV existente
y no a una nueva visita que deba añadirse como duplicado.

No cambiar el formato/esquema de la fila.

## Unidad 2 — IHS4 igual a cero

IHS4 = 0 es un valor clínico válido y NO equivale a dato ausente.

Actualmente existen rutas longitudinales donde el string "0" se descarta.

Auditar de forma acotada el tratamiento de ihs4_actual y campos IHS4 relacionados
en:

- resumen longitudinal;
- dashboard;
- tendencias;
- obtención de último y previo;
- precarga relacionada;
- cualquier helper directamente utilizado por esas superficies.

Corregir los lugares donde 0 se trate incorrectamente como vacío/NR.

No hacer un refactor general de falsy values fuera del alcance.

### Regresión esperada

Una secuencia, por ejemplo:

PV IHS4 = 8
SG IHS4 = 0

debe considerar 0 como la medición más reciente y permitir representar la
mejoría correspondiente.

Dos mediciones válidas:

0 -> 0

deben producir una evolución válida/estable, no "No valorable" por desaparición
de ambas medidas.

## Unidad 3 — Auditoría posterior, REPORT-ONLY

Después de implementar y verificar las unidades 1 y 2, realizar una auditoría
read-only del repositorio completo.

NO corregir automáticamente otros hallazgos.

Crear:

docs/audits/AUDIT_ENFERMERIA_HS_20260924.md

Clasificar hallazgos por severidad/impacto y aportar evidencia concreta.

Revisar especialmente:

- integridad paciente / riesgo de cruce entre pacientes;
- búsqueda por identificadores y codigo_hs;
- importación Excel;
- exportación y correspondencia formulario-columnas;
- valores clínicamente válidos tratados como falsy;
- fechas, orden longitudinal y selección de última/primera visita;
- riesgo de duplicados;
- PV/SG/CX y contaminación de estado entre pestañas;
- sessionStorage/localStorage;
- persistencia accidental de datos identificables;
- llamadas o dependencias externas;
- manejo de Excel vacío, incompleto o con columnas inesperadas;
- errores silenciosos;
- referencias a IDs/campos obsoletos;
- dashboard y resumen longitudinal;
- escape/renderizado de valores procedentes del Excel;
- documentación desalineada respecto al comportamiento real;
- código muerto o ramas imposibles;
- supuestos frágiles detectables en el uso clínico real.

Para cada hallazgo relevante indicar:

- evidencia;
- impacto;
- escenario que lo activa;
- recomendación;
- si requiere decisión de producto o es puramente técnico.

No implementar esos hallazgos en este train salvo que sean necesarios para que
las unidades 1 o 2 funcionen correctamente.

## Documentación

Actualizar únicamente la documentación que quede materialmente obsoleta por
las unidades 1 y 2, especialmente si aplica:

- README.md;
- docs/IMPORTACION_EXCEL.md;
- docs/MANUAL_USUARIO_ENFERMERIA_HS.md;
- docs/PLAN.md.

No regenerar DOCX/PDF salvo que la autoridad actual del repositorio lo exija
explícitamente.

## QA obligatorio

Respetar AGENTS.md.

Como mínimo verificar:

- carga de Excel maestro;
- búsqueda por NUHSA/identificador;
- búsqueda por codigo_hs;
- paciente con PV abre correctamente su PV;
- los datos almacenados de PV reaparecen;
- los detalles no persistidos no se inventan;
- SG sigue funcionando;
- CX sigue funcionando;
- generación/copia de informe;
- generación/copia de fila Excel;
- codigo_hs permanece asociado al paciente correcto;
- nuevo paciente/limpieza no rompe la BD cargada;
- resumen longitudinal;
- dashboard;
- IHS4=0 como último valor;
- IHS4=0 como valor previo;
- tendencia 8 -> 0;
- tendencia 0 -> 0;
- consola sin errores.

Usar evidencia determinista siempre que sea posible.

No introducir un framework de testing pesado únicamente para esta incidencia.

## Delivery boundary

Cambios pequeños y revisables.

Usar Native Gentle para exploración, unidades de trabajo, verificación y review.

No push.
No PR.
No merge.
No force-push.

Finalizar dejando:

- commits locales de las unidades aprobadas;
- árbol limpio;
- informe factual de cambios;
- QA ejecutado y resultados;
- informe de auditoría;
- riesgos o decisiones todavía abiertas.
