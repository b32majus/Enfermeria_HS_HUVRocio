# Código HS — uso operativo

## Objetivo

Definir y estandarizar el uso de `codigo_hs` como identificador pseudonimizado operativo en la herramienta de Enfermería HS.

Permite enlazar visitas longitudinales y preparar la futura vinculación de PROMs remotos sin usar `NUHSA` fuera del circuito clínico interno.

## Formato

Formato obligatorio:

- `HS0001`
- `HS0002`
- `HS0003`
- ...

Reglas:

- Prefijo fijo `HS`.
- Siempre 4 dígitos.
- Sin fechas, sufijos aleatorios, hash ni derivados directos de `NUHSA`.

## Cuándo se reutiliza

Se reutiliza el mismo `codigo_hs` cuando el paciente ya existe en la base cargada y hay código previo en sus registros.

Si aparecen varios códigos HS para el mismo paciente en el histórico, la herramienta avisa y prioriza el código del registro más reciente.

## Cuándo se genera uno nuevo

Se genera un código nuevo cuando el paciente no tiene `codigo_hs` previo.

Estrategia de generación:

- Se detectan códigos existentes (`masterDb` + estado de sesión).
- Se toma el máximo numérico encontrado.
- Se asigna `max + 1`.

Ejemplo:

- existentes: `HS0001`, `HS0002`, `HS0004`
- siguiente: `HS0005`

No se rellenan huecos intermedios.

## Si no hay BD cargada

La herramienta permite generar código HS igualmente para no bloquear la consulta.

Se muestra aviso funcional:

`Sin base cargada: no se puede comprobar si el código ya existe en registros previos.`

## Relación con NUHSA

- `NUHSA` se mantiene como identificador clínico interno de trabajo en la herramienta.
- `codigo_hs` es el identificador pseudonimizado operativo para explotación longitudinal y PROMs remotos futuros.
- `codigo_hs` no sustituye a `NUHSA` en la historia clínica oficial.

## Relación futura con Microsoft Forms

En fases posteriores, `codigo_hs` será el identificador de enlace para respuestas remotas (Microsoft Forms/PROMs), evitando uso de identificadores asistenciales directos en formularios no asistenciales.

## Persistencia y advertencias

- El estado temporal de apoyo se mantiene en `sessionStorage` durante la sesión.
- La persistencia oficial del código ocurre al exportar y pegar la fila en `BD_VISITAS_HS`.
- Recordatorio operativo: copiar siempre la fila Excel para registrar el código en la base.

## Límites

- `codigo_hs` no es identificador oficial del hospital.
- No reemplaza la identificación clínica ni la historia clínica.
- No implica backend ni sincronización automática externa.
