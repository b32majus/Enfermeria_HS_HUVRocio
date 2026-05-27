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

Un paciente que ya tiene `codigo_hs` válido no puede recibir otro desde la interfaz. Los botones de asignación quedan bloqueados cuando ya existe código.

## Cuándo se genera uno nuevo

Se genera automáticamente un código nuevo cuando se inicia o selecciona un paciente por `NUHSA` y no tiene `codigo_hs` previo:

- al confirmar una búsqueda global;
- al introducir un `NUHSA` manualmente en PV, SG o CX y salir del campo;
- al iniciar la precarga de seguimiento;
- como salvaguarda final antes de exportar si todavía faltase el código.

No se genera código mientras únicamente se está escribiendo en el buscador. Los botones de generación/asignación permanecen disponibles como fallback, pero no son necesarios en el flujo normal.

Estrategia de generación:

- Se detectan códigos existentes (`masterDb` + estado de sesión).
- Se toma el máximo numérico encontrado.
- Se asigna `max + 1`.

Ejemplo:

- existentes: `HS0001`, `HS0002`, `HS0004`
- siguiente: `HS0005`

No se rellenan huecos intermedios.

Los códigos generados durante una sesión quedan reservados en `sessionStorage`, aunque se pulse **Nuevo paciente / limpiar formulario**. Por tanto, un código recién generado no se recicla para el siguiente paciente de esa sesión.

## Si no hay BD cargada

La herramienta permite generar código HS igualmente para no bloquear la consulta.

Se muestra aviso funcional:

`Sin BD cargada: no se puede comprobar unicidad histórica. Se reservará el código en esta sesión.`

## Relación con NUHSA

- `NUHSA` se mantiene como identificador clínico interno de trabajo en la herramienta.
- `codigo_hs` es el identificador pseudonimizado operativo para explotación longitudinal y PROMs remotos futuros.
- `codigo_hs` no sustituye a `NUHSA` en la historia clínica oficial.

## Relación futura con Microsoft Forms

En fases posteriores, `codigo_hs` será el identificador de enlace para respuestas remotas (Microsoft Forms/PROMs), evitando uso de identificadores asistenciales directos en formularios no asistenciales.

## Persistencia y advertencias

- El estado temporal de apoyo se mantiene en `sessionStorage` durante la sesión.
- **Nuevo paciente / limpiar formulario** limpia el paciente activo y los formularios PV/SG/CX, pero conserva la base Excel cargada y la reserva de códigos generados en la sesión.
- El código generado queda reservado para evitar duplicados, pero no es oficial hasta copiar y pegar la fila Excel en `BD_VISITAS_HS`.
- Recordatorio operativo: copiar siempre la fila Excel para registrar el código en la base.

## Flujo Seguro

1. Cargar la base `BD_VISITAS_HS`, si está disponible.
2. Buscar al paciente desde el panel global.
3. La herramienta reutiliza automáticamente el código existente o genera el siguiente código disponible.
4. Elegir PV, SG o CX y completar la visita.
5. Registrar la visita y copiar la fila Excel para persistir la correspondencia.

## Límites

- `codigo_hs` no es identificador oficial del hospital.
- No reemplaza la identificación clínica ni la historia clínica.
- No implica backend ni sincronización automática externa.
