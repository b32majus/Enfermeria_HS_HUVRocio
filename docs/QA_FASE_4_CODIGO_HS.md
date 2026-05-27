# QA Fase 4.1 - Estado de paciente y codigo HS

## Objetivo

Validar que la identificacion activa se mantiene coherente entre PV, SG y CX y que `codigo_hs` no puede duplicarse ni reasignarse accidentalmente.

## Archivo de prueba

- `templates/BD_VISITAS_HS_sintetico.xlsx`

## Checklist funcional

1. Abrir `index.html` y cargar la base sintetica.
2. Buscar `TEST0001`; comprobar `TEST0001` / `HS0001` en PV, SG y CX, con botones de asignacion bloqueados.
3. Buscar `TEST0006`; comprobar `TEST0006` / `HS0006` en PV, SG y CX, sin restos de `TEST0001`.
4. Buscar `TEST9999`; comprobar mensaje de no encontrado y que no quedan NUHSA/codigo del paciente anterior.
5. Buscar `TEST9998` y generar codigo; comprobar `HS0007`.
6. Pulsar `Nuevo paciente / limpiar formulario`; confirmar que se limpian buscador, contexto e identificacion PV/SG/CX y que la base sigue cargada.
7. Buscar `TEST9997` y generar codigo; comprobar `HS0008` (no reutiliza `HS0007`).
8. Escribir `hs1` con NUHSA correspondiente; comprobar normalizacion a `HS0001`.
9. Intentar introducir `HS0001` para otro NUHSA; comprobar bloqueo y mensaje de error.
10. Borrar manualmente un codigo ya asignado; comprobar que la interfaz restaura el codigo conocido.
11. Volver a buscar un paciente con codigo generado durante la sesion; comprobar que se reutiliza y no ofrece reasignacion.
12. En Seguimiento, comprobar aviso compacto de paciente activo y que solo permanece la accion `Precargar seguimiento`.

## Checklist de continuidad

- `MASTER_COLUMNS` mantiene 122 columnas y `codigo_hs` conserva su posicion.
- PV, SG y CX exportan una fila de 122 columnas con el codigo correcto.
- `Copiar informe` y `Copiar fila Excel` funcionan.
- No se usa `localStorage`; la base y reservas temporales usan `sessionStorage`.
- No se modifican PROMs ni reaparece `sg_n_visita`.

## Criterio de seguridad

Nunca debe quedar visible o exportable un `NUHSA` combinado con el `codigo_hs` de otro paciente tras una busqueda, precarga o limpieza.
