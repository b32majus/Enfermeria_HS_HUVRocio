# DICCIONARIO_VARIABLES — BD_VISITAS_HS (fase actual)

## Alcance de esta fase

Este diccionario refleja la fase actual previa a la exportación longitudinal única completa.

En esta fase, para PROMs se exportan únicamente **puntuaciones totales**.

No se exportan ítems individuales de DLQI, HADS ni HSQoL-24 salvo instrucción explícita posterior.

## PROMs: licencia, fuente y versionado

### DLQI (Dermatology Life Quality Index)

- Estado en herramienta: incorporado en español.
- Tipo de dato exportado en esta fase:
  - `pv_dlqi_total` (equiv. `pv_dlqi_display`)
  - `sg_dlqi_total` (equiv. `sg_dlqi_display`)
- Fuente documental usada:
  - https://actasdermo.org/es-adaptacion-transcultural-al-espanol-del-articulo-13003408
- Estado de licencia/uso:
  - Instrumento con derechos de uso específicos; revisar condiciones de reutilización/distribución antes de difusión externa.
- Versionado interno recomendado:
  - `dlqi_es_fuente_actasdermo_13003408_v1`

### HADS (Hospital Anxiety and Depression Scale)

- Estado en herramienta: incorporado en español.
- Tipo de dato exportado en esta fase:
  - `pv_hads_a_total` (equiv. `pv_hads_a_display`)
  - `pv_hads_d_total` (equiv. `pv_hads_d_display`)
  - `sg_hads_total_resumen` (equiv. `sg_hads_total_display`, formato `A x | D y`)
- Fuente documental usada:
  - https://vinaros.san.gva.es/documents/3598142/3727212/HADS%20normalizado.doc/c4a07ac9-d7dc-2dd1-6cce-87536490592e?version=2.0&t=1704891278467&targetExtension=pdf&download=true
- Estado de licencia/uso:
  - Puede requerir autorización/licencia para determinados usos; revisar términos antes de difusión externa o comercial.
- Versionado interno recomendado:
  - `hads_es_fuente_gva_v2_20240110`

### HSQoL-24

- Estado en herramienta: incorporado en español.
- Tipo de dato exportado en esta fase:
  - `pv_hsqol24_total` (equiv. `pv_hsqol_display`)
  - `sg_hsqol24_total` (equiv. `sg_hsqol_display`)
- Fuente documental usada:
  - https://www.actasdermo.org/es-desarrollo-validacion-preliminar-del-instrumento-articulo-S0001731019300924
- Estado de licencia/uso:
  - Revisar condiciones de uso/citación para reproducción del cuestionario en material externo.
- Versionado interno recomendado:
  - `hsqol24_es_fuente_actasdermo_S0001731019300924_v1`

## Nota técnica de continuidad

- IHS4 previo y Hurley basal en seguimiento se mantienen manuales temporalmente.
- En fase de carga de Excel maestro deberán precargarse automáticamente desde la última visita del paciente si existe.
- El cálculo de tiempo desde última consulta se valida contra base cuando esté operativa la importación de Excel maestro con datos sintéticos.
