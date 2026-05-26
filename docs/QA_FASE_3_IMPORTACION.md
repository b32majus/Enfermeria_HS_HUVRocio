# QA Fase 3 — Importación Excel maestro y precarga en Seguimiento

## Objetivo de la prueba

Validar funcionalmente que la herramienta permite:

- cargar manualmente una base Excel longitudinal;
- buscar paciente por `NUHSA` o `codigo_hs`;
- precargar campos de seguimiento de forma asistida;
- mantener persistencia temporal durante la sesión con `sessionStorage`;
- perder la base al cerrar pestaña/sesión.

## Archivo de prueba

- `templates/BD_VISITAS_HS_sintetico.xlsx`

## Pasos de prueba manual

1. Abrir la herramienta (`index.html`) en navegador.
2. Ir a la pestaña **Seguimiento**.
3. En el panel superior **Base longitudinal**, pulsar **Cargar Excel maestro** y seleccionar `templates/BD_VISITAS_HS_sintetico.xlsx`.
4. Buscar paciente en la barra `NUHSA o código HS` y pulsar **Buscar última visita**.
5. Verificar la precarga de campos y el bloque **Última visita encontrada**.
6. Probar **Copiar Excel** (PV/SG/CX) para confirmar continuidad de exportación longitudinal.
7. Recargar la pestaña (F5) y comprobar que la base sigue disponible desde `sessionStorage`.
8. Cerrar la pestaña/sesión y abrir de nuevo: comprobar que obliga a cargar de nuevo la base.

## Checklist de pacientes sintéticos

- `TEST0001`: `PV + SG`, mejoría.
- `TEST0002`: `PV + SG + SG`, debe usar la última `SG` previa.
- `TEST0003`: `PV + CX`, debe usar `PV` como última visita clínica para `IHS4`/`Hurley`.
- `TEST0004`: solo `PV`.
- `TEST0005`: `SG` sin `PV` previa, fallback histórico.
- `TEST0006`: `PV + SG` con empeoramiento.
- `TEST9999`: no encontrado.

## Qué se precarga

- fecha última consulta;
- tiempo desde última consulta;
- IHS4 previo;
- Hurley;
- peso previo;
- tabaco previo;
- tratamiento activo previo;
- código HS si existe.

## Qué NO se precarga

- IHS4 actual;
- EVAs actuales;
- brotes actuales;
- PROMs actuales.

## Criterios de aceptación

- El panel de importación es visible al entrar en **Seguimiento**.
- La carga de Excel muestra estado (`Sin base` / `Base cargada`) y fecha/hora.
- La búsqueda encuentra o no encuentra paciente con mensaje claro.
- La precarga no sustituye de forma agresiva campos ya completados sin confirmación.
- `sessionStorage` conserva datos solo en sesión activa.
- Exportación y estructura longitudinal permanecen estables.
