# QA Fase 4.3 - Autogeneracion segura de codigo HS

## Objetivo

Validar que la identificacion activa mantiene coherencia entre PV, SG y CX, que `codigo_hs` se genera automaticamente solo al iniciar paciente y que no puede duplicarse ni reasignarse accidentalmente.

## Archivo de prueba

- `templates/BD_VISITAS_HS_sintetico.xlsx`

## Checklist funcional

1. Abrir `index.html` y cargar la base sintetica.
2. Escribir `TEST9998` sin ejecutar busqueda; comprobar que todavia no se genera codigo.
3. Buscar `TEST0001`; comprobar `TEST0001` / `HS0001` en PV, SG y CX, con botones de asignacion bloqueados y sin codigo nuevo.
4. Buscar `TEST0006`; comprobar `TEST0006` / `HS0006` en PV, SG y CX, sin restos de `TEST0001` ni codigo nuevo.
5. Buscar `TEST9998`; comprobar mensaje de paciente nuevo y autogeneracion `HS0007` en PV, SG y CX.
6. Pulsar `Nuevo paciente / limpiar formulario`; confirmar que se limpian buscador, contexto e identificacion PV/SG/CX y que la base sigue cargada.
7. Buscar `TEST9997`; comprobar autogeneracion `HS0008` (no reutiliza `HS0007`).
8. Escribir `hs1` con NUHSA correspondiente; comprobar normalizacion a `HS0001`.
9. Intentar introducir `HS0001` para otro NUHSA; comprobar bloqueo y mensaje de error.
10. Borrar manualmente un codigo ya asignado; comprobar que la interfaz restaura el codigo conocido.
11. Volver a buscar `TEST9998`; comprobar que recupera `HS0007` y no genera `HS0009`.
12. Introducir manualmente `TEST9996` en PV y salir del campo; comprobar autogeneracion `HS0009` y sincronizacion SG/CX.
13. En Seguimiento, comprobar aviso compacto de paciente activo y que `Precargar seguimiento` mantiene o genera codigo cuando corresponde.
14. Exportar PV con NUHSA y codigo vacio; comprobar autogeneracion antes de copiar la fila.
15. Exportar con codigo HS pero sin NUHSA; comprobar que sigue bloqueado.

## Checklist de continuidad

- `MASTER_COLUMNS` mantiene 122 columnas y `codigo_hs` conserva su posicion.
- PV, SG y CX exportan una fila de 122 columnas con el codigo correcto.
- `Copiar informe` y `Copiar fila Excel` funcionan.
- No se usa `localStorage`; la base y reservas temporales usan `sessionStorage`.
- No se modifican PROMs ni reaparece `sg_n_visita`.
- Sin BD cargada, se permite generar codigo solo al confirmar paciente y se advierte que no puede comprobarse unicidad historica.

## Criterio de seguridad

Nunca debe quedar visible o exportable un `NUHSA` combinado con el `codigo_hs` de otro paciente tras una busqueda, precarga o limpieza.
