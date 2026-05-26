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

## Fase 4 — Refactor modular

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
