# UX Flujo de Consulta — Fase 3.3

## Flujo recomendado de uso

1. Abrir la herramienta.
2. Cargar BD si existe (`Cargar / recargar BD`).
3. Buscar paciente por `NUHSA` o `código HS` en el panel global.
4. Revisar `código HS`:
   - si existe, reutilizarlo;
   - si no existe, asignarlo/generarlo desde el panel global o desde PV/SG/CX.
5. Elegir ruta clínica (Primera Visita, Seguimiento o Cura Post-Qx).
6. Completar la visita actual.
7. Copiar fila Excel (`Copiar fila Excel`).
8. Copiar informe (`Copiar informe`).

## Si no hay BD cargada

- La herramienta sigue funcionando en modo manual.
- Se pueden registrar PV/SG/CX sin precarga.
- El estado del panel mostrará `BD no cargada`.

## Si el paciente no se encuentra

- Se muestra `Paciente no encontrado en la base cargada`.
- Puede continuarse la visita en manual.
- En Seguimiento, se puede introducir `NUHSA`/`código HS` y reintentar precarga.
- Si se trata de paciente nuevo con `NUHSA`, se puede generar `código HS` nuevo de forma explícita.

## Código HS (operativo)

- Formato obligatorio: `HS0001`, `HS0002`, `HS0003`...
- Si el paciente ya tiene código en la base cargada, se reutiliza ese mismo código.
- Si no tiene código, se genera el siguiente disponible (`max + 1`).
- Si no hay BD cargada, la generación sigue disponible pero se avisa que no puede comprobarse unicidad histórica.
- El `código HS` no sustituye al `NUHSA` en la consulta interna; se usa como identificador pseudonimizado para seguimiento/PROMs.
- Para persistir oficialmente el código en la base del proyecto, hay que copiar y pegar la fila Excel exportada.

## Precarga en Seguimiento

Con paciente seleccionado (o identificador manual en SG), `Precargar seguimiento con última visita` puede completar:

- fecha última consulta;
- tiempo desde última consulta (si hay fecha de visita SG actual);
- IHS4 previo;
- Hurley orientativo;
- peso previo;
- tabaco previo;
- tratamiento activo previo;
- código HS (si existe).

## Datos que siempre deben rellenarse en la visita actual

La precarga no sustituye la valoración clínica actual. Deben registrarse en la visita actual, según aplique:

- actividad actual (IHS4 actual, zonas activas);
- EVA actuales;
- brotes y estado global actual;
- PROMs actuales;
- plan y notas de la consulta actual.

## Estado de BD (panel global)

- `BD no cargada`: sin base en la sesión.
- `BD cargada: X registros / Y pacientes`: carga válida.
- `BD cargada con advertencias`: carga válida con columnas faltantes no críticas o columnas extra.

## Semáforos visuales (orientativos)

Todos los semáforos son de lectura rápida y no diagnósticos.

- IHS4:
  - verde: 0-3
  - naranja: 4-10
  - rojo: >=11
- EVA:
  - verde: 0-3
  - naranja: 4-6
  - rojo: 7-10
- HADS (ansiedad/depresión):
  - verde: 0-7
  - naranja: 8-10
  - rojo: >=11
- DLQI:
  - verde: 0-1
  - verde suave: 2-5
  - naranja: 6-10
  - rojo: >=11
- HSQoL-24:
  - basado en interpretación (`Sin impacto/Leve`, `Moderado`, `Severo`) y, si no está disponible, en los rangos internos del formulario.

Título de apoyo aplicado en UI: `Orientativo para priorización/lectura rápida`.
