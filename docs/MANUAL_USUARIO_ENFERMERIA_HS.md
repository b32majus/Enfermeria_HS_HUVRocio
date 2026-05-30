# Manual de usuario - Consulta Enfermería HS HUVR

**Versión beta operativa - mayo 2026**

Herramienta de Consulta de Enfermería HS del Hospital Universitario Virgen del Rocío.

Uso previsto: apoyo al registro estructurado de consulta, generación de informe clínico y construcción de base longitudinal del piloto.

> **Importante:** la herramienta es un apoyo operativo del piloto. No sustituye la historia clínica oficial, los circuitos internos del hospital ni la valoración clínica profesional. Los datos reales deben manejarse solo en ubicaciones autorizadas.

---

## 0. Guía rápida de uso

1. Abrir la herramienta en el navegador.
2. En la barra lateral **Base de datos**, pulsar **Cargar / recargar BD** y seleccionar el Excel correspondiente.
3. Buscar al paciente por **NUHSA** o **código HS**.
4. Si el paciente aparece en la base, pulsar **Ver resumen longitudinal** en la barra lateral para consultar su trayectoria.
5. Si el paciente no existe, la herramienta inicia paciente nuevo y genera `codigo_hs` automáticamente.
6. Elegir la pestaña de trabajo: **Primera Visita**, **Seguimiento** o **Cura Post-Qx**.
7. Completar los campos de la visita actual.
8. En Seguimiento, revisar la precarga automática y usar **Reaplicar precarga** si procede.
9. Revisar la **QuickView de visita actual** para confirmar los datos antes de copiar.
10. Pulsar **Copiar informe** y pegarlo donde corresponda según el circuito asistencial.
11. Pulsar **Copiar fila Excel** y pegarla en la hoja `BD_VISITAS_HS` del Excel maestro.
12. Antes de pasar a otro paciente, pulsar **Nuevo paciente / limpiar formulario**.

> **Regla de oro:** nunca pasar a otro paciente sin buscarlo en la barra lateral o pulsar **Nuevo paciente / limpiar formulario**. Así se evita mezclar NUHSA, código HS o datos clínicos de pacientes distintos.

---

## 1. Archivos entregados

| Archivo | Uso |
|---|---|
| Herramienta HTML | Aplicación que se abre en navegador. Sirve para registrar PV, SG y CX, generar informe y copiar fila Excel. |
| `BD_VISITAS_HS_template.xlsx` | Plantilla vacía para empezar con datos reales. Es la base oficial inicial si el equipo decide comenzar el piloto real. |
| `BD_VISITAS_HS_sintetico.xlsx` | Base de práctica con pacientes ficticios. Sirve para entrenar y probar sin riesgo. Nunca mezclar con datos reales. |
| Manual de usuario | Documento de apoyo para consultar dudas durante la beta. |

### Diferencia entre base sintética y base real

- La base sintética contiene pacientes ficticios tipo `TEST0001`, `TEST0002`, etc. Es solo para practicar.
- La plantilla vacía es la que debe usarse para iniciar el registro real, siempre en ubicación autorizada.
- No copiar filas sintéticas dentro del Excel real.
- No introducir pacientes reales en la base sintética.

---

## 2. Seguridad básica y manejo de datos

- Trabajar con datos reales solo en el ordenador, carpeta o unidad autorizada por el equipo/hospital.
- No enviar Excel con NUHSA por correo personal, mensajería no autorizada o canales externos.
- No usar NUHSA en Microsoft Forms ni en cuestionarios remotos. Para eso se usará el código HS.
- Cerrar la herramienta al terminar la sesión de trabajo.
- Guardar copia de seguridad de la base maestra según indique el equipo responsable.
- La herramienta usa almacenamiento temporal de sesión en el navegador: al cerrar pestaña/sesión, se pierde la base cargada y habrá que cargarla de nuevo.

---

## 3. Barra lateral: Base de datos y paciente

La barra lateral es el punto de entrada de la herramienta. Antes de elegir el tipo de visita, debe usarse para cargar la base y buscar al paciente.

Pasos:

1. Pulsar **Cargar / recargar BD**.
2. Seleccionar el Excel con la hoja `BD_VISITAS_HS`.
3. Comprobar estado de BD y métricas visibles (`registros`, `pacientes`, `códigos HS únicos`).
4. Introducir NUHSA o código HS en el buscador.
5. Pulsar **Buscar paciente** o Enter.

| Situación | Qué hace la herramienta | Qué debe hacer Enfermería |
|---|---|---|
| Paciente existe y tiene código HS | Carga NUHSA, código HS, última visita e IHS4 disponible. | Elegir pestaña y continuar visita. |
| Paciente existe pero no tiene código HS | Genera automáticamente el siguiente código HS disponible. | Continuar y copiar fila Excel al final para persistirlo. |
| Paciente no existe en la base | Inicia paciente nuevo y genera código HS automático al confirmar búsqueda. | Revisar identificación y completar visita. |
| No hay BD cargada | Permite trabajar manualmente, avisando de que no puede comprobar unicidad histórica. | Usar solo si procede y extremar revisión. |

Cuando se encuentra un paciente, la barra lateral muestra una **tarjeta de paciente activo** con NUHSA, código HS y última visita disponible. Desde esa tarjeta puede pulsarse **Ver resumen longitudinal** para abrir un panel flotante con la trayectoria completa del paciente (ver sección siguiente).

El botón **Ver resumen longitudinal** solo aparece cuando hay un paciente activo encontrado en la base. Se oculta al pulsar **Nuevo paciente / limpiar formulario** o al cerrar la sesión.

---

## 4. Resumen longitudinal HS

El **resumen longitudinal** es un panel flotante de solo lectura que muestra la trayectoria del paciente a partir de los datos cargados en la base Excel. Se abre desde la barra lateral pulsando **Ver resumen longitudinal** cuando hay un paciente activo.

### Qué muestra

- **NUHSA y Código HS** — para verificar que el paciente es el correcto.
- **Trayectoria** — número de visitas registradas, primera visita, última visita y tipos de visita disponibles.
- **Actividad clínica** — Hurley basal, IHS4 último, IHS4 previo, tendencia y zonas activas registradas.
- **Tratamiento y seguridad** — tratamiento activo, suspensión prematura, efectos adversos y adherencia si constan en la base.
- **Pendientes** — necesidades a valorar por Dermatología, próxima cita de Enfermería y cura activa si constan.
- **Alertas suaves** — avisos no bloqueantes ante gravedad, empeoramiento, efectos adversos, suspensión, necesidades pendientes o complicaciones si esos datos están registrados.

### Reglas de uso

- Depende de la base Excel cargada: sin BD no hay resumen.
- Se actualiza al cambiar de paciente o al recargar la base.
- No guarda datos ni modifica la herramienta.
- **No sustituye la historia clínica oficial** ni la valoración profesional.
- Sirve como orientación rápida antes y durante la visita.

---

## 5. Código HS

El `codigo_hs` es un identificador pseudonimizado operativo.

Formato:

- `HS0001`
- `HS0002`
- `HS0003`
- ...

Reglas:

- Se genera automáticamente cuando se inicia o selecciona un paciente por NUHSA y no tiene código previo.
- No se genera mientras solo se escribe en el buscador.
- Si el paciente ya tiene código, se reutiliza y no se puede reasignar otro desde la interfaz.
- Los códigos generados durante la sesión quedan reservados aunque se pulse **Nuevo paciente / limpiar formulario**.
- El código no queda oficialmente en la base hasta que se copia la fila Excel y se pega en `BD_VISITAS_HS`.

Ejemplo:

Si la base contiene `HS0001` a `HS0006` y se inicia un paciente nuevo, la herramienta genera `HS0007`. Si se limpia y se inicia otro paciente, generará `HS0008`, no volverá a usar `HS0007`.

---

## 6. Primera Visita (PV)

1. Buscar paciente en la barra lateral o introducir NUHSA en la pestaña PV.
2. Confirmar que aparece NUHSA y código HS en Filiación e historia de la HS.
3. Completar filiación, historia de enfermedad, hábitos, antecedentes y comorbilidades.
4. Registrar exploración, EVAs e IHS4 actual.
5. Completar PROMs si se decide usarlos en consulta.
6. Registrar educación sanitaria realizada, material entregado, necesidades a valorar por Dermatología y notas.
7. Pulsar **Copiar informe**.
8. Pulsar **Copiar fila Excel** y pegar en `BD_VISITAS_HS`.

Campos especialmente importantes:

- NUHSA.
- Código HS.
- Fecha de visita.
- Año de inicio de síntomas.
- Año de diagnóstico.
- Profesional/servicio que deriva.
- Antecedentes familiares.
- Brotes último año.
- Tratamientos previos.
- Deseos genésicos.
- Hábitos.
- Comorbilidades.
- Hurley.
- IHS4.
- EVAs.
- Educación sanitaria.

---

## 7. Seguimiento (SG)

1. Buscar paciente en la barra lateral.
2. Entrar en **Seguimiento**.
3. Revisar el bloque de paciente activo.
4. La herramienta aplica precarga automática si hay base cargada y paciente activo (solo en campos vacíos).
5. Usar **Reaplicar precarga** si se necesita repetir la carga en campos vacíos.
5. Revisar los datos precargados.
6. Completar siempre la valoración actual.
7. Copiar informe y copiar fila Excel.

La precarga puede traer:

- Fecha última consulta.
- Tiempo desde última consulta.
- IHS4 previo.
- Hurley orientativo.
- Peso previo.
- Tabaco previo.
- Tratamiento activo previo.
- Código HS.

La precarga no sustituye la valoración actual. Deben rellenarse siempre, según aplique:

- Actividad actual.
- IHS4 actual.
- EVAs actuales.
- Brotes actuales.
- Tratamiento actual.
- Adherencia.
- Efectos adversos.
- PROMs actuales.
- Plan y notas.

> **Nota documental (tratamiento activo):** pendiente definir con Enfermería/Dermatología/Farmacia si el tratamiento activo se mantiene como registro simple, por grupos terapéuticos o extraído de historia/Farmacia.

---

## 8. Cura Post-Qx (CX)

1. Buscar paciente en la barra lateral o introducir NUHSA en CX.
2. Confirmar NUHSA y código HS.
3. Completar datos de intervención, tipo de cierre, localización, número de cura y fecha.
4. Registrar dolor pre/post cura, analgesia/anestesia si aplica.
5. Completar valoración de herida, limpieza, desbridamiento, apósitos, complicaciones y plan.
6. Copiar informe y copiar fila Excel.

---

## 9. QuickView de visita actual

La **QuickView de visita actual** es un resumen visual de lo que se está registrando en la pestaña activa (PV, SG o CX). Aparece integrada en cada tipo de visita y se actualiza en tiempo real al completar los campos del formulario.

### Qué muestra

- **IHS4 actual** — puntuación y clasificación (LEVE / MODERADO / GRAVE).
- **Síntomas** — EVA dolor, WI-NRS prurito y EVA supuración registrados.
- **Tratamiento y adherencia** — tratamiento activo, cambios, adherencia Morisky y efectos adversos.
- **Curas** — en CX, muestra tipo de herida, apósitos y plan de cura.
- **Alertas** — recordatorios sobre educación, PROMs pendientes o necesidades a valorar por Dermatología.

### Cuándo usarla

- Antes de copiar el informe o la fila Excel, para **confirmar que los datos registrados son correctos**.
- Durante la visita, como **guía rápida** de lo que queda por completar.

### Reglas

- No sustituye completar el formulario ni copiar la fila Excel.
- Depende de los datos introducidos en la pestaña activa.
- Si se cambia de pestaña, la QuickView se actualiza al contenido de la nueva visita.

---

## 10. Dashboard piloto

La herramienta incluye un **dashboard piloto** con indicadores agregados, filtros de cohorte y gráficos simples. Se accede desde el botón **Ver dashboard piloto** en la interfaz principal.

El dashboard es un apoyo operativo del piloto. No sustituye la historia clínica oficial ni debe usarse como análisis científico/publicable sin validación de datos.

### Qué muestra

- **Tarjetas globales** en la cabecera: pacientes únicos, visitas totales, desglose por tipo (PV/SG/CX), pacientes con IHS4 moderado/grave, pacientes con necesidades a valorar por Dermatología y pacientes con tratamiento activo registrado.
- **Distribución IHS4 último** — cuántos pacientes están en nivel Leve, Moderado, Grave o No Registrado.
- **Tabla operativa de pacientes** — una fila por paciente con código HS, última visita, IHS4, tendencia, Hurley, tratamiento activo y alertas. Desde cada fila puede abrirse **Ver resumen** para ver la trayectoria longitudinal completa del paciente.
- **Gráficos simples HTML/CSS**: distribución IHS4, tendencia IHS4, tipos de visita, top tratamientos activos y alertas clínicas.

### Cómo usar los filtros

Los filtros permiten acotar la cohorte que se muestra en las tarjetas, la tabla y los gráficos:

| Grupo de filtro | Opciones |
|---|---|
| **Periodo** | Desde / Hasta, Último mes, Último trimestre, Todo |
| **Tipo de visita** | PV, SG, CX |
| **Actividad** | IHS4 (Leve / Moderado / Grave / NR), Tendencia (Mejoría / Estable / Empeoramiento / NV), Hurley (I / II / III / NR) |
| **Tratamiento y seguridad** | Tratamiento activo, Suspensión prematura, Efectos adversos |
| **Pendientes** | Necesidades Dermatología, Cura activa |

Al aplicar un filtro, la tabla se recalcula automáticamente para mostrar solo los pacientes que cumplen las condiciones seleccionadas. Las tarjetas globales y los gráficos también se actualizan.

### Limpiar filtros

El botón **Limpiar filtros** restaura la vista completa de la cohorte sin ninguna restricción activa.

### Reglas importantes

- El dashboard depende de la base Excel cargada: sin BD cargada no hay dashboard.
- Los datos provienen de la BD cargada en sesión, no de la historia clínica oficial.
- IHS4=0 se considera válido y se clasifica como Leve.
- Los campos vacíos o no registrados se muestran como NR (No Registrado), no como "No".
- El dashboard no guarda datos, no modifica la herramienta ni sustituye la valoración profesional.
- No usar los agregados del dashboard como análisis publicable sin validación manual de los datos fuente.

---

## 11. Copiar informe y copiar fila Excel

**Copiar informe** genera un texto estructurado por bloques numerados, listo para pegar en la historia clínica o donde indique el circuito asistencial.

Características del informe:

- **Texto plano** — sin HTML, markdown ni formato especial, se pega limpio en cualquier sistema de historia clínica.
- **Estructura por bloques** — organizado en secciones numeradas (identificación, historia, clínica, síntomas, educación, plan, etc.).
- **Sin cabecera redundante** — no incluye hospital, fecha, paciente ni NUHSA porque se pega dentro de la historia clínica, donde esos datos ya están.
- **Desglose de zonas activas IHS4** — detalla nódulos, abscesos y fístulas por región anatómica.

**Copiar fila Excel** genera una fila tabulada para pegar en la hoja `BD_VISITAS_HS`.

Reglas:

- Pegar siempre en la siguiente fila libre.
- No modificar cabeceras.
- No reordenar columnas.
- Si la herramienta bloquea exportación por identificación, revisar NUHSA y código HS antes de continuar.

> El código HS, la visita y la información recogida solo quedan oficialmente incorporados a la base cuando se pega la fila Excel en `BD_VISITAS_HS` y se guarda el archivo.
> El informe TXT es un documento de apoyo, no sustituye la fila Excel como fuente estructurada para la base de datos longitudinal.

---

## 12. Nuevo paciente / limpiar formulario

Usar antes de pasar a otro paciente.

La acción:

- Limpia buscador.
- Limpia paciente seleccionado.
- Limpia identificación/registro de PV, SG y CX.
- Mantiene la base Excel cargada durante la sesión.
- Mantiene reservados los códigos generados durante la sesión para evitar duplicados.

---

## 13. Mensajes frecuentes

| Mensaje | Qué significa |
|---|---|
| BD no cargada | No se ha cargado Excel maestro. Puede trabajar manualmente, pero no habrá búsqueda/precarga. |
| Paciente no encontrado | Ese NUHSA/código no está en la base cargada. Si es paciente nuevo, la herramienta generará código HS. |
| Código HS generado automáticamente | La herramienta ha reservado un código nuevo. Hay que copiar la fila Excel para dejarlo registrado. |
| Código HS histórico restaurado | El código introducido no correspondía al NUHSA activo. La herramienta restaura el histórico y exige volver a pulsar `Copiar fila Excel`. |
| No se puede exportar código HS sin NUHSA asociado | Hay código pero falta NUHSA. Revisar identificación antes de exportar. |
| El código pertenece a otro paciente | No usar ese código; buscar el paciente correcto o revisar la base. |

---

## 14. Checklist para beta en consulta

- [ ] La herramienta abre correctamente.
- [ ] La base adecuada está cargada.
- [ ] Se busca paciente antes de completar visita.
- [ ] NUHSA y código HS aparecen coherentes.
- [ ] La visita se completa sin campos confusos.
- [ ] El informe se copia correctamente.
- [ ] La fila Excel se pega correctamente en `BD_VISITAS_HS`.
- [ ] Se pulsa **Nuevo paciente / limpiar formulario** antes de pasar al siguiente paciente.
- [ ] Se anotan incidencias, dudas o campos que sobran/faltan.
- [ ] Comprobar **QuickView de visita actual** en PV/SG/CX.
- [ ] Comprobar **resumen longitudinal** desde la barra lateral.
- [ ] Verificar NUHSA y código HS antes de usar el resumen longitudinal.
- [ ] Comprobar que **limpiar paciente** oculta el resumen longitudinal.
- [ ] Abrir **Ver dashboard piloto** desde la interfaz principal.
- [ ] Comprobar que las tarjetas globales muestran datos coherentes con la BD cargada.
- [ ] Aplicar filtro por tipo de visita y verificar que la tabla y gráficos se actualizan.
- [ ] Aplicar filtro por severidad IHS4 y confirmar la reducción de cohorte.
- [ ] Usar **Limpiar filtros** y comprobar que se restaura la vista completa.
- [ ] Pulsar **Ver resumen** en una fila de la tabla y confirmar que abre el histórico longitudinal del paciente correcto.

---

## 15. Puntos para revisar con el equipo

### Flujo asistencial y agenda

- Confirmar que Enfermería citará 15 minutos antes de Dermatología en HS.
- Confirmar cómo se citarán revisiones: programadas desde la solicitud de Dermatología, no a demanda.
- Confirmar si mañana se probará con pacientes PV, SG, CX o combinación.
- Confirmar quién avisa a Admisión/agenda y si queda pendiente notificar a Elena Baquero.
- Confirmar si habrá una tarea/actividad específica de Enfermería HS para registro homogéneo.

### Base de datos y custodia

- Definir dónde se guardará la base real `BD_VISITAS_HS`.
- Definir quién será responsable de custodiar y guardar el Excel maestro.
- Confirmar si trabajará una sola enfermera/equipo o varios ordenadores.
- Evitar varias copias reales en paralelo sin control.
- Definir rutina de copia de seguridad.

### Formulario y contenido clínico

- Confirmar si la parte quirúrgica/cura postquirúrgica está demasiado extensa y qué simplificar.
- Confirmar si todos los PROMs se usarán en consulta o si algunos pasarán a remoto.
- Confirmar si Hurley se mantiene como orientación enfermera.
- Confirmar si hay campos poco útiles o difíciles de completar en consulta real.

### Tratamiento activo y registro farmacológico

Pendiente clave para Enfermería y Dermatología/Farmacia:

- Confirmar si Enfermería debe registrar tratamiento activo en la herramienta o si se obtendrá de otras fuentes.
- Decidir si el tratamiento se recoge de forma simple, por ejemplo campo libre/listado de fármacos, o de forma estructurada por grupos terapéuticos.
- Si se estructura por grupos, definir qué grupos son útiles:
  - antibióticos;
  - biológicos;
  - tratamiento tópico;
  - hormonal/metabólico;
  - tratamientos para comorbilidades;
  - otros.
- Confirmar si merece la pena que Enfermería recoja fármaco exacto, dosis/frecuencia y cambios, o si esto añade demasiada carga a la consulta.
- Confirmar si parte de la información se puede extraer mejor de Dermatología, de Farmacia Hospitalaria o de la historia clínica.
- Decidir qué dato mínimo sí debe quedar en la herramienta para el seguimiento enfermero: tratamiento activo, cambios desde última visita, suspensión, motivo, adherencia y efectos adversos.
- Acordar si el bloque actual es suficiente para la beta o si se deja como fase posterior.

### Educación sanitaria y apoyo social

- Pedir a Giovanna la validación/revisión definitiva del material de educación sanitaria.
- Definir qué material educativo se entregará realmente al paciente.
- Solicitar a Enfermería el contenido habitual sobre recursos/ayudas sociales que comparten con pacientes.
- Acordar si el documento de apoyo social lo prepara Enfermería, Trabajo Social u otra persona.
- Definir cómo se entregará el material: papel, PDF, QR u otro.

### Salud mental / Psiquiatría

- Confirmar si sigue vigente la necesidad de sesión conjunta con Psiquiatría.
- Definir si habrá señales de alarma o circuito de derivación ante malestar emocional intenso, ideación suicida o riesgo.
- Confirmar si HADS se usará solo como cribado orientativo o como información de apoyo.

### PROMs remotos / Microsoft Forms

- Decidir si el Forms definitivo se crea desde cuenta institucional/de Enfermería.
- Confirmar que el primer campo obligatorio será código HS, nunca NUHSA.
- Definir qué PROMs irán en remoto: DLQI, HSQoL-24, HADS u otros.
- Decidir si se usará un único Forms o varios formularios/QR.
- Definir quién revisa las respuestas y con qué frecuencia.
- Cuando exista el Forms definitivo, revisar el Excel generado antes de integrarlo en la herramienta.

---

## 16. Pendientes derivados del acta del 17 de marzo

| Tema | Pendiente original | Qué decidir ahora |
|---|---|---|
| Circuito de citación | Implantar Enfermería 15 min antes de Dermatología en HS y programar revisiones con paso previo por Enfermería. | Confirmar implantación real y responsable. |
| Agenda específica Enfermería HS | Agenda ya creada/prevista, pendiente de uso estructurado. | Confirmar cómo se activa y quién gestiona huecos. |
| Nueva tarea/actividad | Crear tarea para registrar homogéneamente actividad de Enfermería HS. | Confirmar si se ha solicitado/creado. |
| Formulario HTML/PIDE | Usar HTML operativo y valorar transformación futura a formulario estructurado PIDE. | La herramienta HTML ya está operativa para beta; PIDE queda como evolución. |
| Registro farmacológico | Definir si Enfermería recoge tratamiento activo/fármacos o si se extrae de Dermatología/Farmacia. | Decidir nivel mínimo de registro y si se estructura por grupos terapéuticos. |
| Dossier educación sanitaria | Preparar material educativo para agilizar consulta. | Pendiente validación de Giovanna y definición de contenido final. |
| Material ayudas sociales | Preparar documento de recursos/ayudas sociales para pacientes. | Pedir a Enfermería contenido habitual y responsable de preparación. |
| DLQI/PROMs remotos | Valorar recogida remota para que Enfermería incorpore puntuación. | Fase posterior con Microsoft Forms/QR. |
| Psiquiatría | Valorar sesión conjunta por carga de salud mental en HS. | Confirmar interés, responsable y formato. |

---

## 17. Rutina recomendada para primera beta en consulta

1. Empezar con 1-3 pacientes máximo como prueba controlada.
2. Usar la base real solo si ya está definida la ubicación autorizada; si no, practicar con la sintética.
3. No intentar completar todos los PROMs si la consulta se queda sin tiempo; priorizar flujo e informe.
4. Anotar incidencias concretas: campo, pestaña, paciente ficticio/real, qué ocurrió y qué esperaban que ocurriera.
5. Al finalizar, revisar si pudieron copiar informe, copiar fila Excel y limpiar paciente sin dificultad.

> **Objetivo de la beta:** no demostrar que todo es perfecto. El objetivo es comprobar si la herramienta encaja en el flujo real de consulta y qué ajustes hacen falta antes de escalar el uso.
