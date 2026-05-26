# Plantilla Excel BD_VISITAS_HS

## Ubicación

- Plantilla generada: `templates/BD_VISITAS_HS_template.xlsx`
- Script generador: `scripts/generate_excel_template.py`

## Regeneración

Ejecutar desde la raíz del repositorio:

```bash
python scripts/generate_excel_template.py
```

## Hojas incluidas

- `BD_VISITAS_HS`: hoja maestra de carga, una fila por visita, con cabeceras oficiales.
- `DICCIONARIO_VARIABLES`: tabla de variables y sección PROMs (fuentes/licencia/versionado).
- `INSTRUCCIONES`: guía breve de uso para el equipo.

## Advertencias de uso

- No modificar nombres ni orden de columnas en `BD_VISITAS_HS`.
- No borrar columnas aunque no apliquen a un tipo de visita.
- `tipo_visita` debe usar `PV`, `SG`, `CX`.
- `codigo_hs` está reservado para fase posterior.
- Esta plantilla no sustituye la historia clínica oficial.
- Guardar el archivo en ubicación autorizada por el hospital/equipo.
- No introducir datos identificables fuera del entorno autorizado.
