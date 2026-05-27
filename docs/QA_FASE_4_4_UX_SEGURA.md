# QA Fase 4.4 — UX segura y robustez funcional

## Objetivo

Validar que la herramienta mantiene seguridad de identidad, mejora visibilidad operativa en consulta y no rompe la exportación longitudinal.

## Alcance

- Subfase 4.4A: notificaciones y validaciones de identidad.
- Subfase 4.4B: sidebar BD/paciente.
- Subfase 4.4C: precarga SG y ajustes clínicos/textuales beta.

## Checklist

1. Carga BD operativa desde `Cargar / recargar BD`.
2. Estado lateral muestra `BD no cargada` / `BD cargada correctamente` / `BD cargada con incidencias`.
3. Métricas visibles: registros, pacientes y códigos HS únicos.
4. Si hay códigos HS duplicados en BD, se avisa como incidencia sin romper toda la herramienta.
5. Búsqueda por `NUHSA` y por `codigo_hs` funciona.
6. Paciente activo visible en sidebar (NUHSA, código HS, última visita, IHS4 último).
7. Autogeneración/reutilización de `codigo_hs` mantiene reglas anti-duplicado.
8. Cambio de paciente con datos escritos muestra confirmación explícita.
9. `Nuevo paciente / limpiar formulario` mantiene BD cargada y reserva de códigos de sesión.
10. Precarga SG automática al entrar en pestaña con paciente activo + BD cargada.
11. Precarga SG solo rellena campos vacíos (sin sobrescribir datos escritos).
12. Botón `Reaplicar precarga` funciona en modo campos vacíos.
13. Badge visible: `Datos previos cargados desde última visita`.
14. Toast visible al copiar informe.
15. Toast visible al copiar fila Excel.
16. Error crítico visible en modal (no solo en parte superior).
17. Exportación no bloquea por campos clínicos incompletos.
18. Exportación sí bloquea por incoherencia de identidad.
19. `codigo_hs` sin NUHSA bloquea exportación.
20. `codigo_hs` de otro paciente bloquea exportación.
21. Si se restaura código HS histórico, no exporta automáticamente y requiere segunda pulsación.
22. No existen falsos botones de estado (`Introduzca NUHSA` / `Código HS asignado`).
23. Se mantiene un único buscador global visible en sidebar (sin duplicados confusos).
24. Campo visible `Sexo al nacimiento` aplicado.
25. Campo `Profesional/servicio que deriva` en desplegable con opciones acordadas.
26. `Año inicio síntomas` y `Año diagnóstico` sin asterisco.
27. Campo visual de perímetro abdominal eliminado.
28. Columna `perimetro_abdominal_cm` se mantiene en exportación y se envía vacía.
29. `TA` sin asterisco.
30. Tarjeta de consejo para actividad sedentaria visible cuando aplica.
31. Tarjeta de consejo para alteración de sueño visible cuando aplica.
32. No se modifican textos PROMs.
33. No aparece `sg_n_visita`.
34. `MASTER_COLUMNS` sigue en 122 columnas.
35. PV/SG/CX exportan fila compatible de 122 columnas.
36. Persistencia clínica usa `sessionStorage` (sin `localStorage`).

## Resultado esperado

Consulta más segura y visible en uso real, manteniendo continuidad longitudinal y sin introducir componentes fuera de alcance (Forms/QR/dashboard/backend).
