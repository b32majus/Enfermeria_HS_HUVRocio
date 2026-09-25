# Work Order — PV append-only con versión canónica

Status: EXECUTION_READY
Date: 2026-09-25
Repository: b32majus/Enfermeria_HS_HUVRocio

## Contexto

La herramienta ya permite reabrir una Primera Visita (PV) guardada, editarla y
copiar una nueva fila Excel. Actualmente el mensaje indica que la fila exportada
debe reemplazar manualmente la PV anterior en `BD_VISITAS_HS`.

En el uso real es más seguro no pedir a Enfermería que localice, borre o
sobrescriba una fila existente. El Excel debe poder funcionar de forma
append-only: una corrección se pega al final y la fila anterior se conserva como
trazabilidad.

El esquema ya contiene `fecha_exportacion`, que se genera como timestamp ISO en
cada exportación. No se necesita una columna nueva de "editado" ni un contador de
revisión.

## Autoridad funcional

Para cada paciente existe conceptualmente UNA Primera Visita clínica.

Puede haber varias filas físicas `tipo_visita == PV` porque una PV haya sido
reabierta y corregida. Esas filas son VERSIONES de la misma visita, no visitas
clínicas diferentes.

La PV canónica/vigente es la versión con `fecha_exportacion` válida más reciente.

`fecha_visita` NO determina qué revisión es vigente. Una corrección puede modificar
incluso la propia fecha clínica y debe seguir siendo la versión vigente si su
`fecha_exportacion` es posterior.

No elegir nunca por "fila con más datos".

## Reglas de selección / compatibilidad

Para un conjunto de filas PV del mismo paciente:

1. Una sola PV -> esa es la canónica.
2. Varias PV con `fecha_exportacion` válida -> gana el timestamp más reciente.
3. Una PV con timestamp válido frente a otra sin timestamp o inválido -> gana la
   que tiene timestamp válido.
4. Empate exacto de timestamp -> gana la fila que aparece más tarde en el Excel.
5. Si NINGUNA de las PV tiene `fecha_exportacion` válida -> fallback determinista:
   gana la fila que aparece más tarde en el Excel.
6. Si hay varias PV y se activa el fallback por ausencia de timestamp, al abrir
   la PV mostrar un aviso no bloqueante de que se ha usado la última fila física
   por compatibilidad con datos antiguos.

No migrar filas antiguas y no inventar timestamps.

## Unidad 1 — Helper canónico de versiones PV

Implementar una función pequeña y reusable que, dadas las filas de un paciente,
conserve:

- todas las filas SG;
- todas las filas CX;
- exactamente UNA fila PV: la versión canónica según las reglas anteriores.

Debe preservar el orden clínico posterior por `fecha_visita` como hace hoy
`getPatientRecords`; el orden físico del Excel solo se usa como desempate de
VERSIÓN, no como cronología clínica.

Si se necesita metadata para UI, el helper puede devolver además:

- número de versiones PV físicas;
- si se utilizó fallback legacy;
- timestamp de la versión elegida si existe.

Mantener el cambio pequeño; no introducir una capa de repositorio ni refactor
arquitectónico.

## Unidad 2 — Aplicar la PV canónica a TODA la lógica clínica

Asegurar que las revisiones antiguas de PV no se comportan como visitas clínicas
independientes.

Como mínimo revisar y corregir:

- `getPatientRecords`;
- apertura de `Abrir Primera Visita guardada`;
- selección de última/primera visita del paciente;
- resumen longitudinal;
- IHS4 último/previo y tendencia;
- precarga de Seguimiento;
- Hurley basal;
- dashboard de pacientes;
- contador de visitas y contador PV del dashboard;
- distribución/tendencia IHS4 del dashboard;
- filtros del dashboard que operen sobre visitas;
- cualquier otro consumidor directo de `masterDb` que cuente o interprete PV
  como visita clínica.

El estado de carga de la BD puede seguir mostrando número de FILAS físicas si ya
lo hace; no es necesario redefinir esa métrica. Pero cualquier métrica presentada
como visitas clínicas debe usar la vista canónica.

SG y CX NO se versionan en este work order y no deben deduplicarse.

## Unidad 3 — Flujo append-only al reexportar una PV

Cambiar el aviso actual de "ACTUALIZA/REEMPLAZA" por una instrucción append-only.

Al copiar una PV reabierta debe comunicarse de forma clara, por ejemplo:

"Primera Visita actualizada. Añada esta fila al final de BD_VISITAS_HS. No elimine
ni modifique la fila anterior; la plataforma reconocerá esta exportación como la
versión vigente de la Primera Visita."

No escribir directamente en Excel.
No borrar filas.
No pedir al usuario que sustituya una fila existente.

La nueva fila conserva `tipo_visita = PV` y obtiene el `fecha_exportacion` actual
como ya ocurre hoy.

No añadir columna `editado`, `revision`, `version`, `id_visita` ni similares.

## Unidad 4 — Documentación mínima

Actualizar solo la documentación materialmente afectada, especialmente:

- README.md;
- docs/IMPORTACION_EXCEL.md;
- docs/MANUAL_USUARIO_ENFERMERIA_HS.md;
- docs/PLAN.md.

La documentación debe explicar:

- append-only para correcciones de PV;
- `fecha_exportacion` como autoridad de versión;
- las revisiones anteriores permanecen en Excel pero no cuentan como visitas;
- fallback legacy de última fila física si varias PV antiguas carecen de
  `fecha_exportacion` válida.

No regenerar DOCX/PDF.

## QA obligatorio

Respetar AGENTS.md y mantener los checks existentes.

Añadir verificación determinista para, como mínimo:

1. Una PV -> 1 PV canónica.
2. Dos PV misma `fecha_visita`, timestamps distintos -> gana timestamp más nuevo.
3. Dos PV con distinta `fecha_visita`, pero segunda revisión con timestamp más
   nuevo -> gana timestamp más nuevo aunque la fecha clínica sea anterior/posterior.
4. Timestamp válido vs vacío -> gana válido.
5. Dos PV sin timestamp -> gana la última fila física y se marca fallback legacy.
6. Timestamp idéntico -> gana última fila física.
7. PV antigua IHS4=8 + PV corregida IHS4=6 + SG IHS4=4 -> tendencia clínica usa
   6 -> 4, nunca 8 -> 6 ni cuenta tres visitas.
8. Dashboard con dos versiones PV + un SG -> total visitas clínicas = 2, PV = 1,
   SG = 1.
9. Resumen longitudinal con dos versiones PV + un SG -> nVisitas = 2.
10. Abrir PV -> carga versión canónica.
11. Reexportar PV reabierta -> conserva schema y `fecha_exportacion` nuevo; el
    mensaje instruye añadir al final, no reemplazar.
12. Paciente con PV legacy única sin timestamp sigue funcionando.
13. SG/CX existentes permanecen sin deduplicación.
14. IHS4=0 sigue siendo válido en todas las rutas ya corregidas.
15. node --check PASS.
16. git diff --check origin/main...HEAD PASS.

Usar fixtures/harness temporales si ayuda; no introducir framework pesado.

## Restricciones

NO cambiar `MASTER_COLUMNS`.
NO migrar Excel existente.
NO añadir columnas.
NO borrar o mutar filas cargadas en `masterDb`.
NO backend.
NO localStorage clínico.
NO telemetría.
NO broad refactor.
NO arreglar deuda general del dashboard fuera de lo estrictamente necesario para
que las versiones antiguas de PV no cuenten como visitas.
NO cambiar SG/CX salvo adaptación mínima para consumir la vista canónica.

## Delivery boundary

Trabajo acotado y revisable.
Usar Native Gentle para implementación, verificación y review.
No push.
No PR.
No merge.

Al terminar dejar:

- commits locales;
- árbol limpio;
- QA y resultados;
- review nativa cerrada/acknowledged;
- sin commits documentales posteriores al acknowledgement final.
