# Auditoría Enfermería HS HUVRocío — 2026-09-24

**Tipo:** auditoría REPORT-ONLY (solo informe; ningún hallazgo se ha corregido en este train).
**Ámbito:** repositorio completo en la rama `fix/pv-reopen-ihs4-zero-20260924`, HEAD `a88bf45` (monolito `index.html` ~7.860 líneas, `docs/`, `scripts/`, `templates/`, `vendor/`).
**Motivo:** Unidad 3 de la orden de trabajo `docs/work-orders/2026-09-24-pv-reopen-ihs4-zero-audit.md`, tras implementar y verificar las Unidades 1 (reapertura de PV) y 2 (IHS4=0).
**Método:** exploración read-only del código en HEAD, con verificación determinista (extracción de funciones reales a harness Node) de las rutas corregidas. Evidencia citada por `index.html:línea`.

> **Importante:** este documento es un informe. Los hallazgos NO se han implementado,
> salvo los ya corregidos por las Unidades 1 y 2 antes de esta auditoría
> (reapertura de PV y tratamiento de IHS4=0). Cualquier corrección posterior
> requiere su propia orden de trabajo.

---

## Resumen ejecutivo

- No se detectan fugas externas de datos: sin backend, sin `localStorage`, sin analytics/telemetría, sin `fetch`/`XMLHttpRequest`; SheetJS está vendorizado.
- El riesgo más relevante es la **interpolación sin escapado de valores procedentes del Excel en el panel longitudinal** (F-01): roto de renderizado garantizado y riesgo de inyección si el Excel procede de terceros.
- Existen **valores clínicamente válidos tratados como ausentes** más allá del IHS4 ya corregido: DLQI/HADS confunden "no medido" con 0 en informes (F-05), y las fechas en formato `dd/mm/yyyy` se descartan silenciosamente (F-03), afectando a orden longitudinal, tendencia y dashboard.
- **Riesgo de duplicados de visita**: la alerta de actualización/reemplazo existe solo para PV reabierta; SG/CX pueden duplicar fila para la misma visita (F-04).
- `sessionStorage` contiene la base completa identificable (permitido por AGENTS.md, pero conviene explicitarlo y minimizar) (F-07).

Total: 0 BLOCKER confirmados, 2 ALTO, 6 MEDIO, 5 BAJO, 2 INFO.

---

## Hallazgos

### F-01 · ALTO — Panel longitudinal interpola cadenas del Excel en `innerHTML` sin escapar

- **Evidencia:** `renderLongitudinalQuickView` (~7796-7866): helper `row()` (~7805) concatena `lbl`/`val`; identidad (~7825-7826) usa `model.nuhsa` y `model.codigoHs`; alertas (~7833) `a.text`; filas (~7848-7861) `hurleyBasal`, `zonasActivas`, `ttoActivo`, `efectosAdv`, `necsDerma`, `proxCita`, `curaActiva`. El modelo se construye (~7671-7794) desde filas de `masterDb` (`detalle_efectos_adversos`, `zonas_activas_listado`, `tratamiento_activo`, `fecha_visita`, `tipo_visita`) vía `safeDisplay` (~6611), que no escapa. `dashboardEscapeHtml` (~6957) solo se usa en el dashboard.
- **Impacto:** cualquier celda del Excel con `<`, `&`, `"` rompe el renderizado; con contenido malicioso, inyección de marcado/script en la sesión de la enfermera.
- **Escenario:** cargar un Excel (compartido o editado a mano) con caracteres especiales en notas/detalle EA/tratamiento → seleccionar paciente → "Ver resumen longitudinal".
- **Recomendación:** escapar todo valor interpolado (reutilizar `dashboardEscapeHtml` o un `escapeHtml` común) o construir DOM con `textContent`.
- **¿Decisión de producto?** No (puramente técnico).

### F-02 · ALTO — Dependencia CDN externa (Font Awesome) contradice la regla de no llamadas externas

- **Evidencia:** línea 8: `<link rel="stylesheet" href="https://cdnjs.cloudflare.com/.../font-awesome/6.4.0/css/all.min.css">`. Solo SheetJS está vendorizado.
- **Impacto:** petición saliente en cada carga (privacidad + disponibilidad); los iconos (`fas fa-*`, usados en toda la UI) fallan sin internet o tras proxy hospitalario; contradice AGENTS.md ("no llamadas externas innecesarias") y la percepción de "sin llamadas externas".
- **Escenario:** consulta sin salida a internet o con CDN bloqueado.
- **Recomendación:** vendorizar Font Awesome localmente o sustituir por SVG/emoji inline.
- **¿Decisión de producto?** Sí (política de vendorizado/offline).

### F-03 · MEDIO — `parseVisitDate` no soporta `dd/mm/yyyy` como texto; fechas inválidas se descartan en silencio

- **Evidencia:** ~4751-4770 (usa `new Date(str)`; serial de Excel solo si está disponible); fallback `-1` en ordenaciones (~4795-4798); `dashboardDateInRange` devuelve `false` si la fecha no parsea (~7052-7060).
- **Impacto:** fechas en texto `24/09/2026` se vuelven nulas → "última visita" errónea, IHS4 previo/tendencia incorrectos, y filas desaparecen del dashboard con cualquier filtro de fecha activo.
- **Escenario:** `fecha_visita` editada/pegada como texto en el Excel maestro.
- **Recomendación:** soportar `dd/mm/yyyy` explícitamente; avisar por fila en la importación cuando `fecha_visita` no sea parseable.
- **¿Decisión de producto?** Sí (formato de fecha acordado con el Excel maestro).

### F-04 · MEDIO — La alerta anti-duplicado de visita solo existe en PV reabierta; SG/CX pueden duplicar fila

- **Evidencia:** `exportData_sg` (~6341, copia ~6405) y `exportData_cx` (~6516, copia ~6557) no comprueban visitas existentes; solo PV avisa vía `pvLoadedFromDbRow` (~6030-6036).
- **Impacto:** un SG/CX repetido para la misma visita se cuenta doble en resumen longitudinal, dashboard y en la selección de "última visita".
- **Escenario:** re-copiar la fila de SG/CX tras editar campos, o pulsar copiar dos veces.
- **Recomendación:** extender el aviso de "actualiza/reemplaza" a SG/CX, o detectar `(nuhsa, tipo_visita, fecha_visita)` contra `masterDb` antes de copiar.
- **¿Decisión de producto?** Sí.

### F-05 · MEDIO — DLQI/HADS confunden "no medido" con 0 en informes

- **Evidencia:** `calcDLQI_pv` (~5835-5841) muestra `0` sin respuestas; `calcHADS_pv` (~5858-5862) igual; fallbacks `_f(..., '0')` en `copyReport_pv` (~6047/6080) y `copyReport_sg` (~6494).
- **Impacto:** el informe puede imprimir "DLQI: 0/30" o "HADS 0/21" sin evaluación real — clínicamente engañoso.
- **Escenario:** PROMs en blanco y "Copiar informe".
- **Recomendación:** distinguir sin responder (NR) de 0 real en displays e informes.
- **¿Decisión de producto?** Sí.

### F-06 · MEDIO — Promesas del portapapeles sin manejar en todos los puntos de llamada

- **Evidencia:** `copyTextToClipboard` relanza el error (~3898-3913); los puntos de llamada ignoran la promesa (~6029, 6100, 6405, 6494, 6557, 6604).
- **Impacto:** `unhandled promise rejection` → errores en consola (rompe el QA "consola sin errores"); el portapapeles puede no estar disponible (contexto no seguro, `file://`, permiso denegado).
- **Escenario:** navegador sin API de portapapeles o permiso denegado.
- **Recomendación:** `.catch()` en los puntos de llamada (el toast de fallo ya existe) o dejar de relanzar.
- **¿Decisión de producto?** No.

### F-07 · MEDIO — `sessionStorage` contiene la base completa identificable + mapa NUHSA→código

- **Evidencia:** claves ~3760-3764; `persistMasterDbToSession` serializa todo `masterDb` (~4021), incluidos `nuhsa`, `notas_enfermeria`, zonas, etc.; además `hs_selected_patient` (~4732) y `hs_session_code_map` (~4128).
- **Impacto:** datos clínicos identificables permanecen en el almacenamiento de la pestaña hasta cerrarla (sobreviven a F5). Lo permite AGENTS.md, pero conviene explicitarlo en la documentación de seguridad y valorar minimización.
- **Escenario:** cualquier carga de base.
- **Recomendación:** documentar explícitamente; valorar no persistir texto libre si no es necesario entre recargas.
- **¿Decisión de producto?** Sí (minimización de datos).

### F-08 · MEDIO — Ordenación longitudinal con fechas ausentes usa sentinela `-1`

- **Evidencia:** `getPatientRecords` (~4786-4800), `getLastPatientRecord` (~4804-4815), `getLastClinicalRecord` (~4817-4831), `getDashboardLatestVisit` (~7122-7136).
- **Impacto:** con fechas parcialmente inválidas, las filas sin fecha ordenan primero y pueden excluirse de "última/previa" → IHS4/tendencia/última visita erróneos.
- **Escenario:** mezcla de fechas válidas/inválidas en un mismo paciente.
- **Recomendación:** tratamiento explícito + aviso en importación (relacionado con F-03/F-11).
- **¿Decisión de producto?** No.

### F-09 · MEDIO — Flujos clínicos dependen de `window.confirm` (4 sitios)

- **Evidencia:** ~5013 (cambiar paciente), ~5054 (nuevo paciente), ~5074 (sobrescribir campo), ~8095 (reabrir PV con datos sin guardar).
- **Impacto:** en navegadores kiosco/endurecidos los diálogos pueden suprimirse, con alternativa inexistente pese a disponer de `showCriticalModal`.
- **Escenario:** navegador corporativo configurado para bloquear diálogos.
- **Recomendación:** migrar confirmaciones al componente modal existente.
- **¿Decisión de producto?** Parcial (UX de confirmaciones).

### F-10 · BAJO — Función muerta `getMasterHeader`

- **Evidencia:** definida ~5667, sin puntos de llamada.
- **Impacto:** código muerto; riesgo de deriva de mantenimiento.
- **Recomendación:** eliminar o conectarla.
- **¿Decisión de producto?** No.

### F-11 · BAJO — Importación acepta filas con fechas inválidas/vacías sin aviso por fila

- **Evidencia:** `getRowValueByHeader` (~4176-4182); bucle de filas (~5330-5340); solo `validateImportHeaders` (~5282-5300) avisa a nivel de cabecera.
- **Impacto:** filas malformadas cargan sin aviso → problemas posteriores de fechas/orden (F-03/F-08).
- **Recomendación:** avisar por fila con `fecha_visita` no parseable o `tipo_visita` ausente.
- **¿Decisión de producto?** No.

### F-12 · BAJO — `data-dashboard-patient` escapa solo comillas dobles

- **Evidencia:** ~7587 (`String(p.patientId).replace(/"/g,'&quot;')`); el onclick (~7604) re-busca el paciente, por lo que no hay ejecución directa.
- **Impacto:** bajo; marcado malformado si el identificador contiene `<`/`&`.
- **Recomendación:** escape completo.
- **¿Decisión de producto?** No.

### F-13 · BAJO — Fallo de cuota de `sessionStorage` se comunica de forma efímera

- **Evidencia:** `persistMasterDbToSession` captura el error y llama a `showImportWarnings` (~4024-4026); avisos y éxitos comparten el mismo nodo (~3929-3975).
- **Impacto:** la base funciona en memoria pero no sobrevivirá a recarga, con indicación no persistente.
- **Recomendación:** aviso dedicado y persistente mientras dure la sesión.
- **¿Decisión de producto?** No.

### F-14 · INFO — QuickViews `_qvRow`/`_qvTag` no escapan

- **Evidencia:** ~6644-6654; los valores provienen del formulario y están normalizados numéricamente. Riesgo residual bajo.
- **Recomendación:** escapar de forma defensiva.
- **¿Decisión de producto?** No.

### F-15 · INFO — Deriva documental puntual

- **Evidencia:** `docs/DICCIONARIO_VARIABLES.md:22` declara `fecha_visita` como `YYYY-MM-DD` pero el parser acepta seriales y falla con `dd/mm/yyyy` (F-03); `docs/IMPORTACION_EXCEL.md` no explicita que la base completa (incluido NUHSA) va a `sessionStorage` (F-07); ningún doc menciona el CDN de Font Awesome (F-02). Las afirmaciones sobre IHS4=0 en README/MANUAL/DASHBOARD_V0 son coherentes con el código.
- **Recomendación:** alinear documentación con F-02/F-03/F-07.
- **¿Decisión de producto?** No.

---

## Áreas revisadas sin hallazgos relevantes

- **Escapado en el resto de superficies:** `showCriticalModal` (~3839-3884, `textContent`), `showImportWarnings` (~3929-3957, `createTextNode`), resúmenes de paciente (~4833-4879, `textContent`) son seguros; el dashboard escapa sus campos con `dashboardEscapeHtml` (salvo F-12).
- **IHS4=0:** las rutas corregidas por la Unidad 2 son completas; `parseDashboardIhs4` acepta 0 y los patrones `|| 'NR'` operan sobre strings, por lo que `"0"` se muestra correctamente.
- **Cableado de handlers:** todos los `onclick` inline resuelven a funciones definidas; `exportData_pv/sg/cx` solo se alcanzan vía `exportActiveTab`.
- **IDs de exportación:** los ids `pv_*`, `sg_*`, `cx_*` referenciados por exportación e informes existen en el marcado (muestra de ~30 ids).
- **Rendimiento:** `getPatientRecords` es O(n) pero solo se invoca en acciones discretas, no por tecla; adecuado a escala piloto.
- **SheetJS:** vendorizado; su ausencia se gestiona con mensajes explícitos (~5303-5307, ~5375).
- **Seguridad estructural:** sin `localStorage`, sin analytics/telemetría, sin `fetch`/`XMLHttpRequest` en `index.html`; el mapa de sesión `hs_session_code_map` valida y filtra códigos inválidos al restaurarse (~4132-4148).

## Incertidumbres remanentes

- La severidad real de F-01 depende de si el Excel puede proceder de terceros o ser editado fuera de la herramienta; no se observó contenido malicioso, solo la ruta de interpolación.
- Disponibilidad de `navigator.clipboard` abriendo por `file://` (uso principal documentado en README): depende del navegador; no probado empíricamente aquí.
- No se cruzó de forma exhaustiva cada `getElementById` del archivo completo; el patrón defensivo `if (el)` puede enmascarar referencias obsoletas (clase F-11/F-15).

---

## Cierre

Auditoría generada como REPORT-ONLY el 2026-09-24. Ningún hallazgo de este informe fue corregido en este train. Los hallazgos con decisión de producto pendiente (F-02, F-03, F-04, F-05, F-07, F-09) requieren decisión de la responsable del proyecto antes de cualquier implementación.

---

## Adenda post-review — 2026-09-24 (orden correctiva `docs/work-orders/2026-09-24-pv-reopen-safety-correction.md`)

### D-01 · ALTO — Defecto de integridad agregado/detalle en la reapertura de PV (ENCONTRADO Y CORREGIDO)

- **Origen:** revisión independiente posterior al train de reapertura de PV (revisión nativa del commit `b76e873`). No formaba parte del informe REPORT-ONLY original.
- **Evidencia:** la reapertura de PV (`openExistingPv`) restauraba correctamente los agregados almacenados (IHS4, N/A/F totales, zonas activas, DLQI/HADS/HSQoL-24), pero el detalle no persistido quedaba vacío e interactivo: `updIHS_pv`/`calcTotalIHS_pv` recalculaban desde `pv_ihsData` vacío (índice 5800-5817 del train anterior), `calcDLQI_pv`/`calcHADS_pv`/`updateHSQoL_pv` sobrescribían totales históricos desde respuestas parciales, y QuickView (`buildPvQuickViewModel`), informe (`copyReport_pv`) y exportación (`exportData_pv`) derivaban zonas activas del mapa vacío, llegando a mostrar/exportar "Sin zonas activas" contradictorio con la fila guardada.
- **Impacto:** los informes, la exportación y el recálculo podían contradecir o sobrescribir los agregados históricos almacenados en `BD_VISITAS_HS`.
- **Resolución:** corregido por la orden correctiva `docs/work-orders/2026-09-24-pv-reopen-safety-correction.md` (commits de la rama `fix/pv-reopen-ihs4-zero-20260924`): los agregados almacenados son autoridad en modo histórico PV; los controles de detalle no almacenado quedan desactivados; QuickView/informe/exportación conservan zonas y totales guardados con nota discreta; una PV nueva sigue siendo totalmente interactiva. Sin cambios de esquema Excel.

### D-02 · Seguimiento diferido — hallazgos de dashboard yes/no/actual-vs-histórico

Observados de forma independiente en la misma revisión (no incluidos en el informe original): posibles inconsistencias en el dashboard entre indicadores yes/no y entre valores actuales vs históricos. **Se registran como seguimiento diferido; NO se corrigen en este train** y requieren su propia orden de trabajo con decisión de la responsable del proyecto.

