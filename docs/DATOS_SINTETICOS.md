# Datos Sintéticos Longitudinales

## Ubicación

- Archivo generado: `templates/BD_VISITAS_HS_sintetico.xlsx`
- Script generador: `scripts/generate_synthetic_data.py`

## Regeneración

Desde la raíz del repositorio:

```bash
python scripts/generate_synthetic_data.py
```

## Advertencia

Este archivo contiene **únicamente datos sintéticos** para pruebas técnicas.

No contiene datos reales de pacientes ni identificadores asistenciales reales.

## Cohorte ficticia incluida

- 6 pacientes sintéticos (`TEST0001` a `TEST0006`).
- Códigos HS sintéticos (`HS0001` a `HS0006`).
- Visitas longitudinales PV/SG/CX con coherencia básica de fechas e IHS4.

## Escenarios que permite probar

- Paciente con `PV + SG` y mejoría clínica.
- Paciente con múltiples `SG` y evolución fluctuante.
- Paciente quirúrgico con registro `CX`.
- Paciente con solo `PV` (sin seguimiento posterior).
- Paciente con `SG` sin `PV` previa dentro del Excel (fallback histórico).
- Paciente con `PV + SG` y empeoramiento clínico.

## Uso previsto (fases posteriores)

- Pruebas de carga de Excel maestro.
- Búsqueda por paciente (`nuhsa`, `codigo_hs`).
- Precarga de `ihs4_previo` y `hurley_enfermeria` cuando exista visita previa.
- Cálculo de tiempo desde última consulta.
