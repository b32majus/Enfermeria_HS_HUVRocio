# Dashboard v0 — Enfermería HS HUVRocío

> Documento de diseño funcional. Fase 8A — solo documentación, sin cambios en `index.html`.

---

## 1. Objetivo

El Dashboard v0 busca dar una visión agregada y operativa del piloto de Enfermería HS en el HUVR,
accesible directamente desde la herramienta sin backend ni exportaciones adicionales.

Propósitos concretos:

- Conocer el **volumen de pacientes y visitas** registradas en la base cargada.
- Obtener una visión rápida de la **actividad clínica** del piloto (IHS4, Hurley, zonas activas).
- Identificar **pacientes en seguimiento** con datos incompletos o situaciones de alerta.
- Detectar **pacientes que requieren revisión**: IHS4 grave, empeoramiento, suspensión prematura,
  efectos adversos o necesidades para Dermatología pendientes.
- Apoyar la **comunicación con Dermatología/Giovanna** con datos agregados del piloto.

El dashboard es un apoyo operativo. No es fuente oficial de datos ni sustituye la historia clínica.

---

## 2. Usuarios previstos

| Perfil | Uso esperado |
|---|---|
| Enfermería HS | Consulta rápida de pacientes activos, pendientes y alertas |
| Dermatología / Giovanna | Revisión de evolución del piloto y casos a revisar |
| Equipo de mejora / seguimiento | Monitorización del piloto, detección de brechas de datos |

---

## 3. Principios de diseño

1. **No inventar datos.** Si un campo no existe o está vacío, se muestra como NR, no como cero.
2. **No mostrar métricas si no hay datos suficientes.** Si la BD está vacía o tiene menos de 3 pacientes,
   el dashboard lo indica explícitamente en lugar de mostrar gráficos vacíos.
3. **Diferenciar "0" de "NR".** Un IHS4 de 0 es diferente a un IHS4 no registrado.
4. **Priorizar utilidad clínica sobre estética.** Tarjetas de texto antes que gráficos complejos.
5. **Usar solo datos ya exportados en `BD_VISITAS_HS`.** No acceder a campos no incluidos en
   `MASTER_COLUMNS`.
6. **Mantener el dashboard como apoyo, no como fuente oficial.** Los datos provienen de la BD
   cargada en sesión; no reflejan necesariamente el estado actual del paciente en historia clínica.
7. **No sustituir la historia clínica.** La historia clínica oficial (Iraya/Diraya) es la fuente
   clínica oficial. El panel longitudinal y los informes TXT son apoyos de consulta y registro
   durante la sesión. La `BD_VISITAS_HS` es la fuente estructurada del piloto para el dashboard,
   no un sustituto de la historia clínica.

---

## 4. Estructura propuesta del dashboard

### 4.1 Tarjetas globales

Métricas de resumen en la cabecera del dashboard:

| Tarjeta | Fuente en BD | Notas |
|---|---|---|
| Pacientes únicos | NUHSA únicos en BD | Excluir vacíos |
| Visitas registradas | Total de filas en BD | Todas las visitas |
| Primeras visitas (PV) | `tipo_visita == 'PV'` | |
| Seguimientos (SG) | `tipo_visita == 'SG'` | |
| Curas post-Qx (CX) | `tipo_visita == 'CX'` | |
| Pacientes con IHS4 moderado/grave | Último IHS4 por paciente ≥ 4 | Solo si IHS4 disponible |
| Pacientes con necesidades Dermatología | `necesidades_valorar_dermatologia` no NR | Último registro por paciente |
| Pacientes con tratamiento activo registrado | `tratamiento_activo` no NR | Último registro por paciente |

### 4.2 Actividad clínica

Distribuciones calculadas por paciente (usando última visita disponible con dato válido):

- **Distribución último IHS4:** Leve (< 4) / Moderado (4-10) / Grave (> 10) / NR.
- **Distribución Hurley basal:** I / II / III / NR (usar Hurley de PV preferentemente; fallback primer Hurley disponible).
- **Evolución IHS4 en pacientes con ≥ 2 visitas con IHS4 válido:**
  Mejoría / Estable / Empeoramiento / No valorable.
- **Zonas activas más frecuentes:** campo `zonas_activas_listado` si está presente de forma fiable;
  indicar si hay demasiados NR para calcular.

### 4.3 Seguimiento y seguridad

Calculado sobre el último registro por paciente:

| Métrica | Campo | Tratamiento NR |
|---|---|---|
| Con suspensión prematura | `suspension_prematura` | vacío/NR = No registrado, no No |
| Con efectos adversos | `efectos_adversos` | ídem |
| Con adherencia baja o intermedia | `adherencia_morisky_interpretacion` | mostrar solo si hay valores |
| Con tratamiento activo NR | `tratamiento_activo` vacío o NR | contar por separado |
| Con necesidades Dermatología | `necesidades_valorar_dermatologia` no vacío/NR | |

### 4.4 Curas postquirúrgicas

Calculado solo sobre registros con `tipo_visita == 'CX'`:

| Métrica | Campo |
|---|---|
| Curas registradas | Total filas CX |
| Pacientes con cura activa | `cura_hoy` en última CX |
| Complicaciones registradas | `complicaciones` no vacío/NR en alguna CX |
| Próxima cura pendiente | `proxima_cura` o `frecuencia_cura` no NR en última CX |

### 4.5 Tabla operativa de pacientes

Una fila por paciente, ordenada por última visita descendente.

| Columna | Fuente | Notas |
|---|---|---|
| Código HS | `codigo_hs` | Siempre visible |
| NUHSA | `nuhsa` | Ocultable — por defecto no mostrar; equipo decide |
| Última visita | `fecha_visita` de la última fila | |
| Tipo última visita | `tipo_visita` de la última fila | |
| IHS4 último | `ihs4_actual` del último registro válido | Si NR, mostrar NR |
| Tendencia IHS4 | Calculada: último vs penúltimo válido | Mejoría / Estable / Empeoramiento / NV |
| Hurley basal | `hurley_enfermeria` de PV o primer disponible | |
| Tratamiento activo | `tratamiento_activo` del último registro | |
| Alertas | Generadas según reglas (ver §5) | |
| Acción | Botón "Ver resumen longitudinal" | Usa `openLongitudinalQuickView()` existente |

---

## 5. Reglas de cálculo

### 5.1 Paciente único
- Identificado por `nuhsa` normalizado (función `normalizePatientId` existente).
- Si dos filas tienen el mismo NUHSA normalizado, pertenecen al mismo paciente.

### 5.2 Última visita
- Fila con `fecha_visita` más reciente por paciente (función `getPatientRecords` ya ordena por fecha).

### 5.3 Primera visita
- Fila con `fecha_visita` más antigua por paciente.

### 5.4 IHS4 último
- Último registro por paciente con `ihs4_actual` numérico válido (entero ≥ 0).
- `0` es un valor clínico válido (sin lesiones activas) y debe clasificarse como Leve.
- Vacío, NR, no numérico o no registrado = NR.
- Si no existe ningún IHS4 válido: NR.
- Severidad calculada con `severityFromIhs4(value)` existente: 0-3 = Leve, 4-10 = Moderado, > 10 = Grave.

### 5.5 IHS4 previo
- Penúltimo registro por paciente con `ihs4_actual` numérico válido (entero ≥ 0, incluye 0).
- Si no existe: NR.

### 5.6 Tendencia IHS4
- Requiere IHS4 último y previo numéricos válidos (ambos ≥ 0; 0 es válido).
- Último < Previo → Mejoría.
- Último > Previo → Empeoramiento.
- Último = Previo → Estable.
- Cualquier valor NR (no numérico o no registrado) → No valorable.

### 5.7 Hurley basal
- Priorizar `hurley_enfermeria` del registro con `tipo_visita == 'PV'`.
- Si no hay PV o está vacío: primer `hurley_enfermeria` no vacío encontrado.
- Si ninguno: NR.

### 5.8 Tratamiento activo
- `tratamiento_activo` del último registro del paciente.
- Si vacío: NR.
- No inferir tratamiento de registros anteriores.

### 5.9 Necesidades Dermatología
- `necesidades_valorar_dermatologia` del último registro del paciente.
- Tratar vacío, NR, "no", "ninguno", "no consta" como No registrado (no como No).
- Reutilizar `yesNoFromText` existente para la clasificación binaria si se necesita.

### 5.10 Cura activa
- `cura_hoy` del último registro CX del paciente.
- Reutilizar `yesNoFromText` existente.

### 5.11 Efectos adversos
- `efectos_adversos` del último registro del paciente.
- Reutilizar `yesNoFromText` existente.

### 5.12 Suspensión prematura
- `suspension_prematura` del último registro del paciente.
- Reutilizar `yesNoFromText` existente.

### 5.13 Alertas en tabla
Generar para cada paciente si aplica:

| Alerta | Condición |
|---|---|
| IHS4 grave | Último IHS4 > 10 |
| Empeoramiento | Tendencia == 'Empeoramiento' |
| Suspensión prematura | `yesNoFromText(suspension_prematura)` == 'Sí' |
| Efectos adversos | `yesNoFromText(efectos_adversos)` == 'Sí' |
| Nec. Dermatología | `necesidades_valorar_dermatologia` con valor no NR/No |
| Complicaciones Qx | `yesNoFromText(complicaciones)` == 'Sí' en algún CX |

> Todos estos helpers ya existen en `index.html`: `safeDisplay`, `yesNoFromText`, `severityFromIhs4`,
> `getPatientRecords`, `normalizePatientId`. La implementación debe reutilizarlos directamente.

---

## 6. Estados vacíos y mensajes

| Estado | Comportamiento esperado |
|---|---|
| Sin BD cargada | Mostrar mensaje: "Cargue la base de datos para ver el dashboard." No mostrar tarjetas ni tabla. |
| BD cargada vacía (0 filas) | Mostrar: "La base cargada no contiene visitas registradas." Mostrar solo tarjeta de 0 pacientes. |
| BD con pocos datos (< 3 pacientes) | Mostrar datos pero advertir: "Piloto en fase inicial — los agregados pueden no ser representativos." |
| Campo no disponible en BD | Mostrar NR en la celda correspondiente. No calcular distribuciones si > 50% NR en ese campo. |
| Datos inconsistentes (ej. NUHSA vacío) | Excluir la fila del cálculo y contar como "fila sin identificador". Mostrar aviso si hay muchas. |

---

## 7. Riesgos y límites

- **Datos incompletos durante piloto:** Los primeros meses el registro puede ser parcial. Las métricas
  del dashboard reflejan solo lo que se ha exportado a la BD, no el estado clínico real.
- **Heterogeneidad de registros iniciales:** Diferentes convenciones de texto en campos libres
  (ej. tratamiento activo) pueden dificultar la agregación. No intentar normalizar en el dashboard;
  mostrar el texto tal cual.
- **Riesgo de interpretar NR como No:** Siempre distinguir "vacío/NR" de "explícitamente No".
  Un campo vacío no significa que el paciente no tenga el hallazgo; significa que no fue registrado.
- **No usar para conclusiones científicas sin validación:** El dashboard es operativo. Cualquier
  análisis para publicación requiere validación manual de los datos fuente en la BD maestra.
- **No usar como sustituto de historia clínica:** Los datos del dashboard provienen de la BD cargada
  en sesión. La historia clínica oficial (Iraya/Diraya) es la fuente de verdad.

---

## 8. MVP de implementación — Fase 8B

La primera implementación del dashboard debe incluir solo:

### Incluir en MVP (Fase 8B)

- Tarjetas globales (§4.1).
- Distribución IHS4 último (§4.2, solo esta distribución).
- Tabla operativa de pacientes (§4.5), sin NUHSA visible por defecto.
- Botón "Ver dashboard piloto" en la interfaz principal (ej. en el sidebar o nueva pestaña).
- Enlace / acción "Ver resumen longitudinal" en cada fila de la tabla, reutilizando
  `openLongitudinalQuickView()` existente.

### Dejar fuera para fases posteriores

| Elemento | Motivo |
|---|---|
| Gráficos (barras, líneas, radiales) | Requieren librería de charts, aumentan complejidad |
| Exportación del dashboard | No prioritario para uso operativo inicial |
| Filtros avanzados (por fecha, Hurley, etc.) | Fase posterior cuando haya más datos |
| Power BI / Metabase | Fuera del alcance de la herramienta estática |
| PROMs remotos agregados | Depende de Fase 7 (Microsoft Forms) |
| Análisis estadístico (medias, IC) | Requiere validación científica previa |
| Distribución Hurley, evolución longitudinal | Fase siguiente al MVP |
| Seguimiento y seguridad (§4.3) | Fase siguiente al MVP |
| Curas postquirúrgicas (§4.4) | Fase siguiente al MVP si se registran CX |

---

## 9. QA previsto para Fase 8B

### Checklist de validación funcional

- [ ] BD vacía válida no rompe el dashboard.
- [ ] BD sintética (`BD_VISITAS_HS_sintetico.xlsx`) carga y el dashboard muestra datos.
- [ ] Tarjetas globales calculan correctamente con datos sintéticos.
- [ ] IHS4 = 5 → severidad Moderado.
- [ ] IHS4 = 0 → severidad Leve (no NR).
- [ ] IHS4 última visita 8 → IHS4 previa 5 → Tendencia Empeoramiento.
- [ ] IHS4 última visita 5 → IHS4 previa 8 → Tendencia Mejoría.
- [ ] IHS4 última visita 0 → IHS4 previa 5 → Tendencia Mejoría (0 es válido).
- [ ] Paciente sin IHS4 registrado → columna IHS4 muestra NR.
- [ ] Tabla no duplica pacientes (un paciente con 3 visitas = 1 fila).
- [ ] NUHSA no visible en tabla por defecto.
- [ ] "Ver resumen longitudinal" en tabla abre el panel longitudinal del paciente correcto.
- [ ] Informes TXT (copyReport_*) siguen funcionando sin cambios.
- [ ] Exportaciones Excel (exportData_*) siguen funcionando sin cambios.
- [ ] Sin errores JS en consola durante toda la sesión.
- [ ] Dashboard no modifica MASTER_COLUMNS, carga de BD, validaciones NUHSA/código HS.
