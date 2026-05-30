# PLAN — Evolución herramienta Enfermería HS HUVRocío

## Objetivo

Evolucionar la herramienta de consulta de Enfermería HS desde un prototipo HTML único hacia una herramienta estática, modular y preparada para:

- preconsulta de Dermatología;
- registro enfermero;
- exportación longitudinal;
- futura carga de base Excel;
- futura integración de PROMs remotos.

---

## Fase 0 — Preparación y backup

### Objetivo
Asegurar que la versión actual queda preservada y que se puede probar localmente.

### Tareas
- Clonar el repositorio.
- Crear rama de trabajo si procede.
- Verificar que `index.html` abre correctamente.
- Probar pestañas actuales.
- Probar botones actuales.
- Documentar estructura actual.

### Resultado esperado
Herramienta actual funcionando antes de tocar nada.

---

## Fase 1 — Ajuste clínico visual y campos

### Objetivo
Alinear el formulario con la hoja habitual de Dermatología/Giovanna y limpiar campos innecesarios.

### Tareas
- Añadir leyenda de asteriscos.
- Marcar con `(*)` campos de Dermatología/Giovanna.
- Eliminar:
  - patrón alimentario;
  - dieta;
  - suplementos.
- Mantener ejercicio y sueño como campos simples sin asterisco.
- Cambiar EVAs a botones 0-10.
- Añadir campos faltantes en primera visita:
  - profesional que diagnostica;
  - fiebre;
  - antecedentes familiares;
  - G/O o gineco-obstétricos;
  - deseos genésicos;
  - alcohol UBE/semana;
  - EVA prurito;
  - EVA supuración;
  - material educativo entregado.
- Añadir campos faltantes en seguimiento:
  - fecha última consulta;
  - tiempo desde última consulta;
  - estado global referido;
  - tratamiento realizado desde última visita;
  - suspensión prematura;
  - motivo suspensión;
  - EVA prurito;
  - EVA supuración;
  - síntomas digestivos;
  - síntomas osteoarticulares;
  - síntomas psiquiátricos/salud mental;
  - refuerzo educativo/material si procede.
- Cambiar “Derivaciones” por “Necesidades a valorar por Dermatología”.
- Mantener estructura PROMs con cálculo total, dejando la incorporación del texto completo validado oficial para fase documental/licencias.

### Resultado esperado
Formulario más alineado y limpio, sin cambiar todavía arquitectura profunda.

---

## Fase 2 — IHS4, zonas activas e informe

### Objetivo
Mejorar cálculo clínico y salida textual.

### Tareas
- Añadir cálculo automático:
  - número de zonas activas;
  - listado de localizaciones activas.
- Definición: zona activa = región con nódulos + abscesos + fístulas > 0.
- Mostrar junto al IHS4.
- Incluir en informe.
- Incluir en exportación.
- Reestructurar informes TXT en dos bloques:
  1. RESUMEN PARA DERMATOLOGÍA (*)
  2. REGISTRO AÑADIDO DE ENFERMERÍA

### Resultado esperado
Informe más útil para Giovanna y registro completo de Enfermería.

---

## Fase 2.2 — Plantilla Excel BD_VISITAS_HS

### Objetivo
Disponer de una plantilla Excel oficial para uso de Enfermería y preparación de fases de importación.

### Tareas
- Generar plantilla `templates/BD_VISITAS_HS_template.xlsx`.
- Mantener script reproducible en `scripts/generate_excel_template.py`.
- Incluir hojas:
  - `BD_VISITAS_HS`
  - `DICCIONARIO_VARIABLES`
  - `INSTRUCCIONES`

### Estado de avance
- Plantilla generada y versionada en repositorio.
- Script reproducible disponible para regenerar la plantilla.

### Pendiente para fase posterior
- Carga de datos sintéticos longitudinales.
- Importación Excel maestro.
- Precarga automática de datos en seguimiento.

---

## Fase 2.3 — Datos sintéticos longitudinales

### Objetivo
Generar un archivo sintético longitudinal para pruebas técnicas de importación y precarga, sin usar datos reales.

### Tareas
- Generar `templates/BD_VISITAS_HS_sintetico.xlsx`.
- Mantener script reproducible en `scripts/generate_synthetic_data.py`.
- Incluir cohorte ficticia con escenarios de PV, SG y CX.

### Estado de avance
- Archivo sintético generado con el mismo esquema maestro de 122 columnas.
- Script reproducible disponible para regenerar datos sintéticos.

### Pendiente para fase posterior
- Implementación de importación Excel maestro.
- Uso de `sessionStorage` y precarga automática en la interfaz.

---

## Fase 3 — Exportación longitudinal única

### Objetivo
Unificar la exportación en una única estructura de base de datos.

### Tareas
- Crear esquema único de columnas.
- Exportar siempre la misma estructura desde PV, SG y CX.
- Añadir columna `tipo_visita`.
- Mantener campos vacíos si no aplican.
- Crear o actualizar `docs/DICCIONARIO_VARIABLES.md`.

### Resultado esperado
Cada botón “Copiar Excel” genera una fila compatible con `BD_VISITAS_HS`.

---

## Fase 3 — Importación Excel maestro y precarga temporal

### Objetivo
Permitir carga manual de una base Excel `BD_VISITAS_HS` en una herramienta estática, con almacenamiento temporal de sesión y precarga asistida en seguimiento.

### Tareas
- Cargar `vendor/xlsx.full.min.js` localmente (sin CDN).
- Añadir UI mínima para cargar Excel maestro y mostrar estado de carga.
- Leer hoja `BD_VISITAS_HS` y validar columnas críticas.
- Guardar registros en memoria y `sessionStorage` de la pestaña.
- Recuperar base cargada al recargar dentro de la misma sesión.
- Buscar pacientes por `nuhsa` o `codigo_hs`.
- Precargar en Seguimiento:
  - fecha última consulta;
  - tiempo desde última consulta;
  - IHS4 previo;
  - Hurley orientativo;
  - peso previo;
  - tabaco previo;
  - tratamiento activo previo.
- Validar flujo con `templates/BD_VISITAS_HS_sintetico.xlsx`.

### Resultado esperado
La pestaña de Seguimiento puede apoyarse en una base longitudinal temporal, sin backend y sin persistencia tras cerrar la pestaña.

### Estado
- Validación manual completada y documentada en `docs/QA_FASE_3_IMPORTACION.md`.

---

## Refactor modular (pospuesto)

### Objetivo
Separar HTML, CSS y JS sin cambiar comportamiento.

### Tareas pendientes
- Extraer CSS a `assets/css/styles.css`.
- Extraer JS común a módulos:
  - config;
  - state;
  - utils;
  - ihs4;
  - reports;
  - export.
- Mantener compatibilidad con GitHub Pages.
- No introducir frameworks.

### Resultado esperado
Código más mantenible, sin cambio funcional.

### Prioridad
Baja (pospuesto) — el refactor modular amplio queda en espera mientras la herramienta está en uso piloto real. Cualquier cambio debe hacerse en el `index.html` monolítico actual. No iniciar refactor sin aprobación explícita.

---

## Fase 3.3 — UX global y limpieza visual

### Objetivo
Clarificar el flujo real de consulta (cargar base, buscar paciente y luego elegir tipo de visita) y mejorar legibilidad sin cambiar la lógica clínica de fondo ni la exportación longitudinal.

### Tareas
- Mover carga de BD y búsqueda de paciente a un panel global compacto.
- Mantener `sessionStorage` y búsqueda longitudinal ya existente.
- Dejar en Seguimiento un bloque reducido de paciente seleccionado y acción de precarga.
- Quitar comportamiento sticky de la barra inferior de acciones.
- Renombrar acciones a texto clínico-operativo más claro.
- Rediseñar componente EVA (botones más legibles, separación y ayuda visual).
- Eliminar EVAs duplicadas dentro de PROMs.
- Añadir semáforos orientativos en:
  - IHS4;
  - DLQI;
  - HADS;
  - HSQoL-24;
  - EVA.

### Resultado esperado
Flujo recomendado visible desde el inicio de la herramienta, menor carga visual en consulta y lectura clínica rápida con indicadores orientativos.

---

## Fase 4 — Código HS automático

### Objetivo
Incorporar generación y reutilización operativa de `codigo_hs` como identificador pseudonimizado interno del proyecto, manteniendo el flujo clínico actual sin backend.

### Tareas
- Normalizar entradas de código HS a formato `HS0001`.
- Reutilizar `codigo_hs` si el paciente ya existe en la base cargada.
- Generar `codigo_hs` nuevo (`max + 1`) cuando el paciente no tenga código previo.
- Integrar la asignación/revisión de código en panel global y en PV/SG/CX.
- Mantener exportación longitudinal sin cambios de columnas ni orden.
- Mantener estado temporal en `sessionStorage` para apoyo durante la sesión.

### Estado de avance
- Lógica de normalización, validación y asignación de código integrada en la UI.
- Reutilización automática al encontrar paciente con histórico.
- Generación manual asistida para pacientes nuevos sin código.

### Hotfix Fase 4.1 — Estado de paciente y código HS
- Sincronizar `NUHSA` y `codigo_hs` como una única identidad activa en PV/SG/CX.
- Limpiar contexto y formularios al iniciar nuevo paciente, conservando la BD cargada.
- Bloquear reasignación de códigos existentes y duplicados entre NUHSA.
- No reciclar en la misma sesión códigos generados y todavía pendientes de consolidar en Excel.
- Simplificar la indicación de paciente activo en Seguimiento.

### Pendiente para fase posterior
- Integración con Microsoft Forms.
- Flujo QR para PROMs remotos.
- Importación de PROMs remotos vinculados por `codigo_hs`.

---

## Fase 4.3 — Autogeneración segura de código HS

### Objetivo
Evitar que la asignación operativa de `codigo_hs` dependa de recordar pulsar un botón, preservando las reglas anti-duplicado, anti-reescritura y la reserva temporal de sesión.

### Tareas
- Reutilizar o generar automáticamente `codigo_hs` al confirmar el inicio/selección de paciente.
- No generar mientras solo se escribe en el buscador.
- Integrar la salvaguarda en búsqueda global, entrada manual de NUHSA, precarga SG y exportación.
- Mantener botones de generación como fallback.
- Mantener `MASTER_COLUMNS`, PROMs, importación Excel y persistencia exclusiva en `sessionStorage`.

### Resultado esperado
Todo paciente con `NUHSA` activo dispone de `codigo_hs` antes de exportar, sin duplicar ni reescribir códigos y con recordatorio de persistencia en `BD_VISITAS_HS`.

---

## Fase 4.4 — Ajustes beta consulta: UX segura y robustez funcional

### Objetivo
Corregir problemas detectados en consulta real para mejorar visibilidad de estados críticos, minimizar bloqueos no clínicos y reforzar seguridad operativa sin modificar la estructura longitudinal de exportación.

### Subfase 4.4A — Notificaciones y validaciones de identidad
- Sustituir alertas no visibles por toasts y modal crítico.
- Mantener bloqueo de exportación solo por identidad (`NUHSA`/`codigo_hs`) e incoherencias de paciente activo.
- Corregir conflictos de `codigo_hs` sin perder formulario y sin exportación automática tras corrección.

### Subfase 4.4B — Sidebar de base y paciente
- Mantener en lateral el estado de BD, buscador y paciente activo.
- Mostrar métricas de carga (`registros`, `pacientes`, `códigos HS únicos`) y avisos de incidencias por duplicados de código.
- Mantener confirmación explícita al cambiar de paciente con formularios ya rellenados.

### Subfase 4.4C — Seguimiento y ajustes clínicos/textuales
- Precarga automática en Seguimiento al entrar en pestaña con paciente activo + BD cargada.
- Precarga solo sobre campos vacíos (sin sobrescribir entrada de Enfermería).
- Botón secundario de `Reaplicar precarga` para campos vacíos.
- Ajustes de formulario beta:
  - `Sexo` -> `Sexo al nacimiento`.
  - `Año inicio síntomas` y `Año diagnóstico` sin asterisco.
  - `Profesional/servicio que deriva` como desplegable.
  - eliminación visual de perímetro abdominal.
  - `TA` sin asterisco.
  - consejos breves no culpabilizantes para sedentarismo y alteración del sueño.
- Mantener tratamiento activo sin módulos terapéuticos nuevos; decisión de detalle pendiente con Enfermería/Dermatología/Farmacia.

### Restricciones de la fase
- No modificar `MASTER_COLUMNS`.
- No modificar PROMs.
- No introducir Forms, QR, dashboard, backend ni refactor modular.

---

---

## Estado actual tras uso piloto real

### Implantado y validado en consulta real

La herramienta ha entrado en uso piloto real en la consulta de Enfermería HS del HUVR. Las siguientes fases están implantadas y validadas:

- **Fase 4.4 UX segura, identidad robusta y QA** — toasts, modales críticos, validación de identidad, sidebar de base y paciente, precarga automática en seguimiento, ajustes clínicos/textuales beta.
- **Fase 4.5 Sidebar visual** — panel lateral con estado de BD, buscador y paciente activo.
- **Corrección WI-NRS prurito** — etiqueta corregida en componente EVA.
- **Hotfix BD vacía válida** — carga de Excel maestro sin registros previos ya no bloquea la herramienta.

### Pendiente inmediato (próximas fases)

Las fases que siguen reflejan el orden de prioridad actual tras la entrada en piloto real.

---

## Fase 5 — Carga Excel maestro y sessionStorage

### Estado: IMPLANTADA Y VALIDADA EN USO REAL / Vigilancia post-implantación

### Objetivo
Permitir carga manual de Excel maestro y precarga de datos previos durante la sesión.

### Tareas implantadas
- Vendorizar SheetJS en `/vendor/xlsx.full.min.js`.
- Botón “Cargar Excel maestro” en panel global.
- Lectura de hoja `BD_VISITAS_HS` con validación de columnas críticas.
- Guardado de base cargada en memoria y `sessionStorage`.
- Recuperación de base cargada al recargar dentro de la misma sesión.
- Búsqueda de paciente por NUHSA o codigo_hs.
- Precarga de última visita en seguimiento:
  - fecha última consulta;
  - IHS4 previo;
  - peso previo;
  - Hurley basal si existe;
  - tabaco previo;
  - comorbilidades conocidas;
  - último tratamiento registrado si existe.
- Cálculo de tiempo desde última consulta si hay fecha previa.
- **Soporte de BD vacía válida** — la herramienta no bloquea si el Excel cargado no tiene registros previos.
- **Reemplazo seguro de BD previa** — al cargar un nuevo Excel, la base previa se reemplaza sin residuos en sessionStorage.

### Vigilancia post-implantación
- Confirmar que la búsqueda por NUHSA/codigo_hs funciona con bases de tamaño real.
- Verificar que la precarga no sobrescribe datos introducidos por Enfermería en la sesión actual.

---

## Fase futura — Tratamiento estructurado (opcional)

### Objetivo
Estructurar tratamiento por módulos, sin sobrecargar la interfaz de Enfermería.

### Tareas futuras
- Mantener en fases iniciales campos simples de tratamiento:
  - tratamiento realizado desde última visita;
  - tratamiento activo;
  - suspensión prematura y motivo;
  - adherencia/Morisky;
  - efectos adversos.
- Diseñar módulos opcionales para evolución posterior:
  - antibiótico;
  - biológico;
  - tópico;
  - hormonal/metabólico;
  - comorbilidades/otros.

### Resultado esperado
Mayor granularidad de tratamiento sin comprometer usabilidad en consulta.

---

## Fase 6 — Código anónimo HS

### Estado: IMPLANTADA Y VALIDADA MANUALMENTE / Vigilancia de duplicidades

### Objetivo
Crear identificador simple para PROMs remotos y seguimiento pseudonimizado.

### Tareas implantadas
- Campo `codigo_hs` integrado en formularios y exportación.
- Generación automática en formato `HS0001`, `HS0002`, `HS0003`...
- Reutilización de código existente si el paciente ya tiene histórico en la base cargada.
- Prevención de duplicados — no se asigna un código ya existente a otro paciente.
- Bloqueo de incoherencias NUHSA/código HS — no permite cambiar NUHSA si el código ya está ligado a otro paciente.
- Exportación de `codigo_hs` en la hoja maestra `BD_VISITAS_HS`.

### Vigilancia post-implantación
- Confirmar que no se generan duplicados entre sesiones (consolidación manual en Excel).
- Verificar que el formulario Microsoft Forms recibe códigos válidos.

---

## Fase 6.5 — Rediseño de informes TXT para historia clínica

### Objetivo
Rediseñar los informes de texto plano (PV, SG, CX) para que sean compatibles con el volcado directo en la historia clínica (Iraya/Diraya), sin asumir formato HTML, negritas ni maquetación web.

### Tareas
- Rediseñar informe de Primera Visita (PV):
  - texto plano sin HTML;
  - estructura clínica ordenada: motivo de consulta, antecedentes, exploración, IHS4, EVAs, necesidades a valorar por Dermatología, registro añadido de Enfermería;
  - incluir zonas activas detalladas con nódulos, abscesos y fístulas por región anatómica;
  - incluir totales IHS4.
- Rediseñar informe de Seguimiento (SG):
  - mismo formato texto plano;
  - reflejar cambios desde última visita;
  - incluir IHS4 actual vs previo si existe;
  - zonas activas detalladas.
- Rediseñar informe de Cura Post-Qx (CX):
  - mismo formato texto plano;
  - incluir localización, tipo de cura, complicaciones si aplica.
- Validar que el informe se puede copiar y pegar directamente en Iraya/Diraya sin pérdida de información.

### Dependencias
- Confirmar con el servicio de informática del HUVR si Iraya/Diraya acepta algún marcado mínimo o requiere texto plano estricto.

---

## Fase 7 — PROMs remotos vía Microsoft Forms (pospuesta)

### Prioridad actual
Baja — se retoma tras estabilizar informe TXT (Fase 6.5), QuickViews (Fase 7A) y Dashboard v0 (Fase 8).

### Objetivo
Preparar conexión con cuestionarios domiciliarios.

### Tareas futuras
- Crear sección PROMs remotos.
- Añadir QR único al Microsoft Forms.
- Primer campo obligatorio del Forms: `codigo_hs`.
- Importar Excel de respuestas del Forms.
- Vincular respuestas por `codigo_hs`.
- Mostrar últimos PROMs remotos encontrados.

### Resultado esperado
Integración remota sin identificadores directos.

---

## Fase 7A — QuickViews clínicos

### Objetivo
Proporcionar vistas rápidas de indicadores clave durante la consulta, sin necesidad de exportar ni abrir el Excel maestro.

### Tareas
- QuickView paciente activo:
  - última visita (fecha y tipo);
  - IHS4 previo;
  - Hurley;
  - DLQI previo;
  - tratamiento activo;
  - EVAs previas (dolor, prurito, supuración).
- QuickView brotes:
  - número de brotes desde última visita;
  - localización.
- QuickView curas:
  - tipo y localización de curas activas;
  - próxima cura programada si aplica.
- Integrar en panel lateral o cabecera de cada pestaña.
- No requerir recarga de Excel para actualizar (leer de base en memoria).

---

## Fase 8A — Diseño funcional Dashboard v0

### Estado: DISEÑO COMPLETADO / Sin cambios en código

### Objetivo
Documentar el diseño funcional del Dashboard v0 operativo antes de implementar código.
Esta fase no toca `index.html` ni ningún archivo de la herramienta.

### Tareas completadas
- Documento de diseño creado en `docs/DASHBOARD_V0.md`.
- Estructura del dashboard definida: tarjetas globales, actividad clínica, seguimiento y seguridad,
  curas postquirúrgicas, tabla operativa de pacientes.
- Reglas de cálculo definidas para todos los indicadores.
- Reutilización de helpers existentes (`safeDisplay`, `yesNoFromText`, `severityFromIhs4`,
  `getPatientRecords`, `normalizePatientId`, `openLongitudinalQuickView`) documentada.
- Estados vacíos y mensajes definidos.
- Riesgos y límites documentados.
- MVP de Fase 8B delimitado.
- Checklist de QA previsto documentado.

### Restricciones de la fase
- No modifica `index.html`.
- No modifica `MASTER_COLUMNS`.
- No modifica exportaciones ni informes.
- No introduce localStorage.
- No hace merge hasta revisión del diseño.

---

## Fase 8B — Implementación Dashboard v0 MVP

### Estado: IMPLEMENTADA (MVP) / Pendiente validación visual final en Pages

### Objetivo
Implementar el MVP del dashboard operativo definido en `docs/DASHBOARD_V0.md`.

### Alcance del MVP (ver §8 de DASHBOARD_V0.md)
- Tarjetas globales: pacientes únicos, visitas totales, PV/SG/CX, pacientes con IHS4 moderado/grave,
  pacientes con necesidades Dermatología, pacientes con tratamiento activo.
- Distribución IHS4 último: Leve / Moderado / Grave / NR.
- Tabla operativa de pacientes: una fila por paciente, con Código HS, última visita, tipo, IHS4,
  tendencia, Hurley, tratamiento, alertas y acción "Ver resumen longitudinal".
- Botón de acceso al dashboard en la interfaz principal.
- NUHSA no visible en tabla por defecto.

### Restricciones
- No modificar `MASTER_COLUMNS`.
- No modificar `exportData_*` ni `copyReport_*`.
- No modificar carga de BD ni validaciones NUHSA/código HS.
- No introducir localStorage.
- Solo `index.html`.
- Reutilizar helpers existentes al máximo.

### Fuera del MVP
Gráficos de barras/líneas, filtros avanzados, exportación del dashboard, PROMs remotos agregados,
análisis estadístico, Power BI. Ver `docs/DASHBOARD_V0.md §8` para detalle completo.

### Prioridad
Completada como MVP. Mantener vigilancia de QA funcional y visual en entorno Pages.

---

## Fase 8C — Dashboard v0: filtros y gráficos simples

### Estado: PENDIENTE / No iniciar hasta validar visualmente Fase 8B en Pages

### Objetivo
Evolucionar el dashboard desde resumen MVP a analítica básica con filtros de cohorte y visualización agregada simple.

### Alcance previsto
- Filtros por periodo (desde/hasta, último mes, último trimestre, todo).
- Filtros por tipo de visita (PV/SG/CX).
- Filtros de actividad (IHS4, tendencia, Hurley).
- Filtros de tratamiento y seguridad (tratamiento activo, suspensión prematura, efectos adversos).
- Filtros de pendientes (necesidades Dermatología, cura activa).
- Gráficos simples HTML/CSS: distribución IHS4, tendencia IHS4, tipos de visita, top tratamientos activos, alertas clínicas.

### Restricciones
- No modificar `masterDb`.
- No interpretar NR como No.
- IHS4=0 se considera válido y leve.
- Sin librerías externas en primera iteración (sin Chart.js).
- Sin exportación de dashboard en Fase 8C.
