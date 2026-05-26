# DICCIONARIO_VARIABLES — BD_VISITAS_HS (exportación longitudinal unificada)

## Alcance

Este diccionario define la estructura única de columnas para la hoja maestra `BD_VISITAS_HS`.

Reglas de esta fase:

- Todas las pestañas (PV, SG, CX) exportan **exactamente** las mismas columnas y orden.
- `tipo_visita` usa valores: `PV`, `SG`, `CX`.
- Si una variable no aplica a un tipo de visita, se exporta vacía.
- No se recupera `sg_n_visita`.
- IHS4 previo y Hurley basal en seguimiento se mantienen manuales de forma temporal.

## Esquema maestro de columnas

| variable | etiqueta | tipo | opciones/formato | aplica_a | origen | observaciones |
|---|---|---|---|---|---|---|
| fecha_exportacion | Fecha/hora de exportación | datetime texto | ISO-8601 | PV,SG,CX | Sistema | Momento de copia al portapapeles |
| version_herramienta | Versión herramienta | texto | `v1.3-longitudinal-export` | PV,SG,CX | Sistema | Control de trazabilidad |
| tipo_visita | Tipo de visita | texto | `PV` / `SG` / `CX` | PV,SG,CX | Sistema | Clasificador principal longitudinal |
| fecha_visita | Fecha de visita | fecha | `YYYY-MM-DD` | PV,SG,CX | Dermatología/Giovanna | Fecha asistencial |
| nuhsa | NUHSA | texto | libre | PV,SG,CX | Dermatología/Giovanna | Identificador asistencial local |
| codigo_hs | Código anónimo HS | texto | `HS0001...` | PV,SG,CX | Sistema | Fase futura; en esta fase puede ir vacío |
| edad | Edad | número | entero | PV | Dermatología/Giovanna | Vacío en SG/CX si no se captura |
| sexo | Sexo | texto | opciones del formulario | PV | Dermatología/Giovanna | Vacío en SG/CX si no se captura |
| situacion_laboral | Situación laboral | texto | opciones del formulario | PV | Enfermería | |
| anio_inicio_sintomas | Año inicio síntomas | número | año (4 dígitos) | PV | Dermatología/Giovanna | |
| anio_diagnostico | Año diagnóstico | número | año (4 dígitos) | PV | Dermatología/Giovanna | |
| retraso_diagnostico | Retraso diagnóstico | texto | libre/calculado visible | PV | Calculado | Se mantiene el formato mostrado en UI |
| profesional_diagnostico | Profesional que diagnostica | texto | libre | PV | Dermatología/Giovanna | |
| antecedentes_familiares_hs | Antecedentes familiares HS | texto | Sí/No/Desconocido | PV | Dermatología/Giovanna | |
| brotes_ultimo_anio | Brotes último año | número | entero | PV | Dermatología/Giovanna | Campo específico de brotes en Primera Visita |
| n_medicos_previos | Nº médicos previos | número | entero | PV | Enfermería | Número de profesionales consultados antes del diagnóstico |
| fiebre_brotes | Fiebre asociada a brotes | texto | Sí/No/No preguntado | PV | Dermatología/Giovanna | |
| urgencias_hs_ultimo_anio | Urgencias HS último año | número | entero | PV | Dermatología/Giovanna | |
| cirugias_previas_hs | Cirugías previas HS | número | entero | PV | Dermatología/Giovanna | |
| biologico_previo | Biológico previo | texto | opciones del formulario | PV | Dermatología/Giovanna | |
| tratamientos_previos_relevantes | Tratamientos previos relevantes | texto | libre | PV | Dermatología/Giovanna | |
| gine_obst | Antecedentes gineco-obstétricos | texto | libre | PV | Dermatología/Giovanna | |
| deseos_genesicos | Deseos genésicos | texto | opciones del formulario | PV | Dermatología/Giovanna | |
| tabaco_estado | Estado tabaco | texto | No/Ex/Activo/etc | PV,SG | Dermatología/Giovanna | |
| cigarrillos_dia | Cigarrillos por día | número | entero | PV | Dermatología/Giovanna | |
| anios_fumando | Años fumando | número | entero | PV | Dermatología/Giovanna | |
| intentos_cesacion | Intentos cesación | texto | No,1,2-3,>3 | PV | Enfermería | |
| alcohol_ube_semana | Alcohol UBE/semana | número | entero | PV | Dermatología/Giovanna | |
| ejercicio | Actividad física/ejercicio | texto | opciones del formulario | PV,SG | Enfermería | |
| sueno | Sueño | texto | opciones del formulario | PV,SG | Enfermería | |
| peso_kg | Peso | número | decimal | PV,SG | Dermatología/Giovanna | |
| talla_m | Talla | número | decimal (m) | PV,SG | Dermatología/Giovanna | |
| imc | IMC | número/texto | decimal | PV,SG | Calculado | |
| perimetro_abdominal_cm | Perímetro abdominal | número | cm | PV | Dermatología/Giovanna | |
| ta | Tensión arterial | texto | p.ej. `130/85` | PV | Dermatología/Giovanna | |
| hta | Hipertensión | texto | Sí/No | PV | Enfermería | Derivado de selección de comorbilidades |
| dislipemia | Dislipemia | texto | Sí/No | PV | Enfermería | Derivado de selección de comorbilidades |
| diabetes | Diabetes | texto | Sí/No | PV | Enfermería | Derivado de selección de comorbilidades |
| obesidad | Obesidad | texto | Sí/No | PV | Enfermería | Derivado de selección de comorbilidades |
| acne_grave_conglobata | Acné grave/conglobata | texto | Sí/No | PV | Enfermería | Derivado de selección de comorbilidades |
| sinus_pilonidal | Sinus pilonidal | texto | Sí/No | PV | Enfermería | Derivado de selección de comorbilidades |
| eii_digestivo | EII/síntomas digestivos | texto | Sí/No/No preguntado | PV,SG | Enfermería | PV desde comorbilidades; SG desde seguimiento |
| espa_osteoarticular | EspA/síntomas osteoarticulares | texto | Sí/No/No preguntado | PV,SG | Enfermería | PV desde comorbilidades; SG desde seguimiento |
| ansiedad_dx | Ansiedad diagnosticada | texto | Sí/No | PV | Enfermería | Derivado de selección de comorbilidades |
| depresion_dx | Depresión diagnosticada | texto | Sí/No | PV | Enfermería | Derivado de selección de comorbilidades |
| malestar_emocional_alto | Malestar emocional alto | texto | Sí/No | PV | Enfermería | Derivado de selección de comorbilidades |
| sop | SOP | texto | Sí/No | PV | Enfermería | Derivado de selección de comorbilidades |
| brotes_perimenstruales | Brotes perimenstruales | texto | Sí/No | PV | Enfermería | Derivado de selección de comorbilidades |
| aco_actual | ACO actual | texto | Sí/No | PV | Enfermería | Derivado de selección de comorbilidades |
| hurley_enfermeria | Hurley registrado por Enfermería | texto | I/II/III | PV,SG | Enfermería | En SG se mantiene manual temporal |
| ihs4_previo | IHS4 previo | número | entero | SG | Enfermería | Manual temporal hasta importación de maestro |
| ihs4_actual | IHS4 actual | número | entero | PV,SG | Calculado | |
| gravedad_ihs4 | Gravedad IHS4 | texto | Leve/Moderado/Grave | PV,SG | Calculado | |
| nodulos_total | Nódulos totales | número | entero | PV,SG | Calculado | |
| abscesos_total | Abscesos totales | número | entero | PV,SG | Calculado | |
| fistulas_total | Fístulas totales | número | entero | PV,SG | Calculado | |
| zonas_activas_n | Número de zonas activas | número | entero | PV,SG | Calculado | Zona activa: N+A+F > 0 |
| zonas_activas_listado | Listado de zonas activas | texto | separado por comas | PV,SG | Calculado | |
| eva_dolor | EVA dolor | número/texto | 0-10 | PV,SG | Dermatología/Giovanna | |
| eva_prurito | EVA prurito | número/texto | 0-10 | PV,SG | Dermatología/Giovanna | |
| eva_supuracion | EVA supuración | número/texto | 0-10 | PV,SG | Dermatología/Giovanna | |
| fecha_ultima_consulta | Fecha última consulta | fecha | `YYYY-MM-DD` | SG | Dermatología/Giovanna | |
| tiempo_desde_ultima_consulta | Tiempo desde última consulta | texto | libre/auto (`días` y `meses`) | SG | Calculado | Validación con base maestra en fase futura |
| estado_global_referido | Estado global referido | texto | Mejor/Igual/Peor/No registrado | SG | Dermatología/Giovanna | |
| brotes_desde_ultima_visita | Brotes desde última visita | número | entero | SG | Dermatología/Giovanna | |
| tratamiento_realizado_desde_ultima | Tratamiento desde última visita | texto | libre | SG | Dermatología/Giovanna | |
| tratamiento_activo | Tratamiento activo | texto | lista separada por `;` | SG | Enfermería | Sin módulos terapéuticos en esta fase |
| suspension_prematura | Suspensión prematura | texto | No/Sí/No aplica | SG | Dermatología/Giovanna | |
| motivo_suspension | Motivo suspensión | texto | libre | SG | Dermatología/Giovanna | |
| adherencia_morisky_score | Score Morisky | número/texto | 0-4 | SG | Calculado | |
| adherencia_morisky_interpretacion | Interpretación Morisky | texto | Alta/Intermedia/Baja/Pendiente | SG | Calculado | |
| adherencia_morisky_patron | Patrón Morisky | texto | Completo/Incompleto | SG | Calculado | |
| efectos_adversos | Efectos adversos | texto | lista separada por `;` | SG | Enfermería | |
| detalle_efectos_adversos | Detalle efectos adversos | texto | libre | SG | Enfermería | |
| autoinyeccion | Autoinyección | texto | opciones del formulario | SG | Enfermería | |
| rotacion_punto_inyeccion | Rotación punto inyección | texto | opciones del formulario | SG | Enfermería | |
| almacenamiento_correcto | Almacenamiento correcto | texto | opciones del formulario | SG | Enfermería | |
| dlqi_total | DLQI total | número/texto | 0-30 | PV,SG | PROM | Solo total |
| hads_ansiedad_total | HADS ansiedad total | número/texto | 0-21 | PV,SG | PROM | Solo total |
| hads_depresion_total | HADS depresión total | número/texto | 0-21 | PV,SG | PROM | Solo total |
| hsqol24_total | HSQoL-24 total | número/texto | 0-96 | PV,SG | PROM | Solo total |
| hsqol24_interpretacion | Interpretación HSQoL-24 | texto | Sin impacto/Leve/Moderado/Severo | PV,SG | PROM | |
| impacto_sexual | Impacto sexual | texto | opciones del formulario | PV | Enfermería | |
| aislamiento_social | Aislamiento social | texto | opciones del formulario | PV | Enfermería | |
| educacion_sanitaria_realizada | Educación sanitaria realizada | texto | lista separada por `;` | PV | Enfermería | Intervenciones marcadas |
| material_educativo_entregado | Material educativo entregado | texto | No/Sí | PV | Enfermería | |
| refuerzo_educativo_realizado | Refuerzo educativo realizado | texto | No/Sí | PV,SG | Enfermería | En SG desde campo de refuerzo |
| cura_hoy | ¿Cura hoy? | texto | No/Si | SG | Cura | |
| tipo_herida | Tipo de herida (cura SG) | texto | opciones del formulario | SG | Cura | |
| aposito_cura | Apósito usado en cura SG | texto | libre | SG | Cura | |
| notas_cura | Notas de cura SG | texto | libre | SG | Cura | |
| n_cura | Número de cura | número | entero | CX | Cura | |
| fecha_intervencion | Fecha intervención | fecha | `YYYY-MM-DD` | CX | Cura | |
| cirujano | Cirujano | texto | libre | CX | Cura | |
| tipo_intervencion | Tipo intervención | texto | opciones del formulario | CX | Cura | |
| tipo_cierre | Tipo cierre | texto | opciones del formulario | CX | Cura | |
| localizacion_qx | Localización quirúrgica | texto | opciones del formulario | CX | Cura | |
| eva_pre_cura | EVA pre-cura | número | 0-10 | CX | Cura | |
| eva_post_cura | EVA post-cura | número | 0-10 | CX | Cura | |
| analgesia_pre_cura | Analgesia pre-cura | texto | opciones del formulario | CX | Cura | |
| anestesia_local | Anestesia local | texto | opciones del formulario | CX | Cura | |
| hstime | Valoración HS-TIME | texto | resumen componentes T/I/M/E | CX | Cura | |
| herida_dimensiones | Dimensiones herida | texto | `largo x ancho x profundidad` | CX | Cura | |
| herida_color_lecho | Color lecho | texto | opciones del formulario | CX | Cura | |
| herida_olor | Olor | texto | opciones del formulario | CX | Cura | |
| piel_perilesional | Piel perilesional | texto | opciones del formulario | CX | Cura | |
| sangrado | Sangrado | texto | opciones del formulario | CX | Cura | |
| solucion_limpieza | Solución limpieza | texto | opciones del formulario | CX | Cura | |
| desbridamiento | Desbridamiento | texto | opciones del formulario | CX | Cura | |
| lavado_h2o2 | Lavado H2O2 | texto | No/Si | CX | Cura | |
| aposito_primario | Apósito primario | texto | opciones del formulario | CX | Cura | |
| aposito_secundario | Apósito secundario | texto | opciones del formulario | CX | Cura | |
| fijacion | Fijación | texto | opciones del formulario | CX | Cura | |
| tpn | TPN | texto | No/Sí | CX | Cura | |
| complicaciones | Complicaciones | texto | lista separada por `;` | CX | Cura | |
| detalle_complicaciones | Detalle complicaciones | texto | libre | CX | Cura | |
| foto_clinica | Foto clínica | texto | No/Sí | CX | Cura | |
| proxima_cura | Próxima cura | fecha | `YYYY-MM-DD` | CX | Cura | |
| frecuencia_cura | Frecuencia cura | texto | opciones del formulario | CX | Cura | |
| necesidades_valorar_dermatologia | Necesidades a valorar por Dermatología | texto | libre | PV,SG | Enfermería | Mantener esta nomenclatura |
| proxima_cita_enfermeria | Próxima cita enfermería | fecha | `YYYY-MM-DD` | PV,SG | Enfermería | |
| notas_enfermeria | Notas de Enfermería | texto | libre | PV,SG,CX | Enfermería | |

## PROMs: fuente, licencia y versionado interno

En esta fase, la exportación longitudinal incluye **solo puntuaciones totales** de PROMs.

No se exportan ítems individuales de DLQI, HADS ni HSQoL-24 salvo instrucción explícita posterior.

### DLQI (Dermatology Life Quality Index)

- Variables en maestro:
  - `dlqi_total`
- Fuente documental usada:
  - https://actasdermo.org/es-adaptacion-transcultural-al-espanol-del-articulo-13003408
- Licencia/uso:
  - Instrumento con derechos de uso específicos; revisar condiciones antes de difusión externa.
- Versionado interno recomendado:
  - `dlqi_es_fuente_actasdermo_13003408_v1`

### HADS (Hospital Anxiety and Depression Scale)

- Variables en maestro:
  - `hads_ansiedad_total`
  - `hads_depresion_total`
- Fuente documental usada:
  - https://vinaros.san.gva.es/documents/3598142/3727212/HADS%20normalizado.doc/c4a07ac9-d7dc-2dd1-6cce-87536490592e?version=2.0&t=1704891278467&targetExtension=pdf&download=true
- Licencia/uso:
  - Puede requerir autorización/licencia según uso; revisar términos antes de difusión externa o comercial.
- Versionado interno recomendado:
  - `hads_es_fuente_gva_v2_20240110`

### HSQoL-24

- Variables en maestro:
  - `hsqol24_total`
  - `hsqol24_interpretacion`
- Fuente documental usada:
  - https://www.actasdermo.org/es-desarrollo-validacion-preliminar-del-instrumento-articulo-S0001731019300924
- Licencia/uso:
  - Revisar condiciones de uso/citación para reproducción en material externo.
- Versionado interno recomendado:
  - `hsqol24_es_fuente_actasdermo_S0001731019300924_v1`

## Nota técnica de continuidad

- `ihs4_previo` y `hurley_enfermeria` en seguimiento se mantienen manuales en esta fase.
- En fase de importación de Excel maestro se precargarán desde la última visita registrada, si existe.
