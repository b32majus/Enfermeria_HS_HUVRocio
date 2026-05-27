# Manual de usuario - Consulta Enfermería HS HUVR

**Versión beta operativa - mayo 2026**

Herramienta de Consulta de Enfermería HS del Hospital Universitario Virgen del Rocío.

Uso previsto: apoyo al registro estructurado de consulta, generación de informe clínico y construcción de base longitudinal del piloto.

> **Importante:** la herramienta es un apoyo operativo del piloto. No sustituye la historia clínica oficial, los circuitos internos del hospital ni la valoración clínica profesional. Los datos reales deben manejarse solo en ubicaciones autorizadas.

---

## 0. Guía rápida de uso

1. Abrir la herramienta en el navegador.
2. En el panel superior **Base y paciente**, pulsar **Cargar / recargar BD** y seleccionar el Excel correspondiente.
3. Buscar al paciente por **NUHSA** o **código HS**.
4. Si el paciente no existe, la herramienta inicia paciente nuevo y genera `codigo_hs` automáticamente.
5. Elegir la pestaña de trabajo: **Primera Visita**, **Seguimiento** o **Cura Post-Qx**.
6. Completar los campos de la visita actual.
7. En Seguimiento, usar **Precargar seguimiento** si procede.
8. Pulsar **Copiar informe** y pegarlo donde corresponda según el circuito asistencial.
9. Pulsar **Copiar fila Excel** y pegarla en la hoja `BD_VISITAS_HS` del Excel maestro.
10. Antes de pasar a otro paciente, pulsar **Nuevo paciente / limpiar formulario**.

> **Regla de oro:** nunca pasar a otro paciente sin buscarlo en el panel superior o pulsar **Nuevo paciente / limpiar formulario**. Así se evita mezclar NUHSA, código HS o datos clínicos de pacientes distintos.

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

## 3. Panel superior: Base y paciente

El panel superior es el punto de entrada de la herramienta. Antes de elegir el tipo de visita, debe usarse para cargar la base y buscar al paciente.

Pasos:

1. Pulsar **Cargar / recargar BD**.
2. Seleccionar el Excel con la hoja `BD_VISITAS_HS`.
3. Comprobar que aparece **BD cargada: X registros / Y pacientes**.
4. Introducir NUHSA o código HS en el buscador.
5. Pulsar **Buscar paciente** o Enter.

| Situación | Qué hace la herramienta | Qué debe hacer Enfermería |
|---|---|---|
| Paciente existe y tiene código HS | Carga NUHSA, código HS, última visita e IHS4 disponible. | Elegir pestaña y continuar visita. |
| Paciente existe pero no tiene código HS | Genera automáticamente el siguiente código HS disponible. | Continuar y copiar fila Excel al final para persistirlo. |
| Paciente no existe en la base | Inicia paciente nuevo y genera código HS automático al confirmar búsqueda. | Revisar identificación y completar visita. |
| No hay BD cargada | Permite trabajar manualmente, avisando de que no puede comprobar unicidad histórica. | Usar solo si procede y extremar revisión. |

---

## 4. Código HS

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

## 5. Primera Visita (PV)

1. Buscar paciente en el panel superior o introducir NUHSA en la pestaña PV.
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
- Profesional que diagnostica.
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

## 6. Seguimiento (SG)

1. Buscar paciente en el panel superior.
2. Entrar en **Seguimiento**.
3. Revisar el bloque de paciente activo.
4. Pulsar **Precargar seguimiento** si hay base cargada y visita previa.
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

---

## 7. Cura Post-Qx (CX)

1. Buscar paciente en el panel superior o introducir NUHSA en CX.
2. Confirmar NUHSA y código HS.
3. Completar datos de intervención, tipo de cierre, localización, número de cura y fecha.
4. Registrar dolor pre/post cura, analgesia/anestesia si aplica.
5. Completar valoración de herida, limpieza, desbridamiento, apósitos, complicaciones y plan.
6. Copiar informe y copiar fila Excel.

---

## 8. Copiar informe y copiar fila Excel

**Copiar informe** genera texto narrativo/estructurado para pegar donde indique el circuito asistencial.

**Copiar fila Excel** genera una fila tabulada para pegar en la hoja `BD_VISITAS_HS`.

Reglas:

- Pegar siempre en la siguiente fila libre.
- No modificar cabeceras.
- No reordenar columnas.
- Si la herramienta bloquea exportación por identificación, revisar NUHSA y código HS antes de continuar.

> El código HS, la visita y la información recogida solo quedan oficialmente incorporados a la base cuando se pega la fila Excel en `BD_VISITAS_HS` y se guarda el archivo.

---

## 9. Nuevo paciente / limpiar formulario

Usar antes de pasar a otro paciente.

La acción:

- Limpia buscador.
- Limpia paciente seleccionado.
- Limpia identificación/registro de PV, SG y CX.
- Mantiene la base Excel cargada durante la sesión.
- Mantiene reservados los códigos generados durante la sesión para evitar duplicados.

---

## 10. Mensajes frecuentes

| Mensaje | Qué significa |
|---|---|
| BD no cargada | No se ha cargado Excel maestro. Puede trabajar manualmente, pero no habrá búsqueda/precarga. |
| Paciente no encontrado | Ese NUHSA/código no está en la base cargada. Si es paciente nuevo, la herramienta generará código HS. |
| Código HS generado automáticamente | La herramienta ha reservado un código nuevo. Hay que copiar la fila Excel para dejarlo registrado. |
| Código HS asignado | El paciente ya tiene código; no debe generarse otro. |
| No se puede exportar código HS sin NUHSA asociado | Hay código pero falta NUHSA. Revisar identificación antes de exportar. |
| El código pertenece a otro paciente | No usar ese código; buscar el paciente correcto o revisar la base. |

---

## 11. Checklist para beta en consulta

- [ ] La herramienta abre correctamente.
- [ ] La base adecuada está cargada.
- [ ] Se busca paciente antes de completar visita.
- [ ] NUHSA y código HS aparecen coherentes.
- [ ] La visita se completa sin campos confusos.
- [ ] El informe se copia correctamente.
- [ ] La fila Excel se pega correctamente en `BD_VISITAS_HS`.
- [ ] Se pulsa **Nuevo paciente / limpiar formulario** antes de pasar al siguiente paciente.
- [ ] Se anotan incidencias, dudas o campos que sobran/faltan.

---

## 12. Puntos para revisar con el equipo

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
- Revisar si el bloque de tratamiento activo es suficiente o si más adelante habrá que estructurarlo por grupo terapéutico.
- Confirmar si hay campos poco útiles o difíciles de completar en consulta real.

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

## 13. Pendientes derivados del acta del 17 de marzo

| Tema | Pendiente original | Qué decidir ahora |
|---|---|---|
| Circuito de citación | Implantar Enfermería 15 min antes de Dermatología en HS y programar revisiones con paso previo por Enfermería. | Confirmar implantación real y responsable. |
| Agenda específica Enfermería HS | Agenda ya creada/prevista, pendiente de uso estructurado. | Confirmar cómo se activa y quién gestiona huecos. |
| Nueva tarea/actividad | Crear tarea para registrar homogéneamente actividad de Enfermería HS. | Confirmar si se ha solicitado/creado. |
| Formulario HTML/PIDE | Usar HTML operativo y valorar transformación futura a formulario estructurado PIDE. | La herramienta HTML ya está operativa para beta; PIDE queda como evolución. |
| Dossier educación sanitaria | Preparar material educativo para agilizar consulta. | Pendiente validación de Giovanna y definición de contenido final. |
| Material ayudas sociales | Preparar documento de recursos/ayudas sociales para pacientes. | Pedir a Enfermería contenido habitual y responsable de preparación. |
| DLQI/PROMs remotos | Valorar recogida remota para que Enfermería incorpore puntuación. | Fase posterior con Microsoft Forms/QR. |
| Psiquiatría | Valorar sesión conjunta por carga de salud mental en HS. | Confirmar interés, responsable y formato. |

---

## 14. Rutina recomendada para primera beta en consulta

1. Empezar con 1-3 pacientes máximo como prueba controlada.
2. Usar la base real solo si ya está definida la ubicación autorizada; si no, practicar con la sintética.
3. No intentar completar todos los PROMs si la consulta se queda sin tiempo; priorizar flujo e informe.
4. Anotar incidencias concretas: campo, pestaña, paciente ficticio/real, qué ocurrió y qué esperaban que ocurriera.
5. Al finalizar, revisar si pudieron copiar informe, copiar fila Excel y limpiar paciente sin dificultad.

> **Objetivo de la beta:** no demostrar que todo es perfecto. El objetivo es comprobar si la herramienta encaja en el flujo real de consulta y qué ajustes hacen falta antes de escalar el uso.
