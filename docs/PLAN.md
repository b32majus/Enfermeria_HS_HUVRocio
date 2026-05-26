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

## Fase futura — Refactor modular

### Objetivo
Separar HTML, CSS y JS sin cambiar comportamiento.

### Tareas
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

### Pendiente para fase posterior
- Integración con Microsoft Forms.
- Flujo QR para PROMs remotos.
- Importación de PROMs remotos vinculados por `codigo_hs`.

---

## Fase 5 — Carga Excel maestro y sessionStorage

### Objetivo
Permitir carga manual de Excel maestro y precarga de datos previos durante la sesión.

### Tareas
- Vendorizar SheetJS en `/vendor/xlsx.full.min.js`.
- Añadir botón “Cargar Excel maestro”.
- Leer hoja `BD_VISITAS_HS`.
- Guardar base cargada en memoria y `sessionStorage`.
- Buscar paciente por NUHSA o codigo_hs.
- Precargar última visita en seguimiento:
  - fecha última consulta;
  - IHS4 previo;
  - peso previo;
  - Hurley basal si existe;
  - tabaco previo;
  - comorbilidades conocidas;
  - último tratamiento registrado si existe.
- Calcular tiempo desde última consulta si hay fecha previa.
- Validar cálculo automático de tiempo desde última consulta con datos sintéticos tras la importación.

### Resultado esperado
La herramienta ayuda a recuperar datos previos sin backend y sin persistencia tras cierre de sesión.

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

### Objetivo
Crear identificador simple para PROMs remotos.

### Tareas
- Añadir campo `codigo_hs`.
- Añadir botón “Generar código HS”.
- Formato:
  - HS0001
  - HS0002
  - HS0003
- Si el paciente ya existe en base cargada, reutilizar código.
- Si no existe, generar siguiente código disponible.
- Exportar `codigo_hs` en hoja maestra.
- No usar NUHSA en formularios remotos.

### Resultado esperado
Cada paciente tiene un código anónimo fácil de escribir en Microsoft Forms.

---

## Fase 7 — PROMs remotos vía Microsoft Forms

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

## Fase 8 — Dashboard futuro

### Objetivo
Visualizar seguimiento paciente y resultados agregados.

### Tareas futuras
- Vista paciente:
  - última visita;
  - IHS4 previo/actual;
  - EVAs;
  - brotes;
  - tratamiento;
  - PROMs;
  - curas.
- Vista proyecto:
  - número de visitas;
  - primeras/seguimientos/curas;
  - educación sanitaria;
  - evolución IHS4;
  - evolución DLQI;
  - necesidades detectadas.

### Resultado esperado
Dashboard clínico-operativo y de publicación.
