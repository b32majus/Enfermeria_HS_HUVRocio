# Consulta Enfermería HS · HUVR

> Herramienta web estática para apoyar la consulta de Enfermería EPA en Hidradenitis Supurativa del Hospital Universitario Virgen del Rocío.

**Estado:** beta operativa  
**Uso:** piloto asistencial controlado  
**Arquitectura:** HTML estático + Excel longitudinal + almacenamiento temporal de sesión  
**Última fase:** informes TXT ordenados y QuickViews clínicas (visita actual + resumen longitudinal)

---

## Qué permite hacer

La herramienta estructura tres circuitos de trabajo:

| Circuito | Uso principal |
|---|---|
| **Primera Visita (PV)** | Filiación, historia de HS, hábitos, comorbilidad, exploración, IHS4, PROMs y educación sanitaria. |
| **Seguimiento (SG)** | Evolución, precarga de datos previos, IHS4 actual, tratamiento, adherencia, efectos adversos, PROMs y notas. |
| **Cura Post-Qx (CX)** | Registro de curas postquirúrgicas, dolor, herida, apósitos, complicaciones y plan de cura. |

Genera:

- informe clínico estructurado para copiar;
- fila Excel longitudinal para pegar en `BD_VISITAS_HS`;
- código HS pseudonimizado operativo para seguimiento y futura vinculación de PROMs remotos;
- QuickView de la visita actual (PV/SG/CX) para revisar antes de copiar el informe;
- resumen longitudinal del paciente desde la BD cargada, accesible desde la barra lateral.

---

## Flujo recomendado

1. Abrir la herramienta.
2. Cargar la base Excel desde **Base y paciente**.
3. Buscar paciente por `NUHSA` o `codigo_hs`.
4. La herramienta reutiliza o genera automáticamente el `codigo_hs`.
5. Si el paciente aparece en la base, revisar **Ver resumen longitudinal** desde la barra lateral para consultar su trayectoria.
6. Elegir pestaña: **Primera Visita**, **Seguimiento** o **Cura Post-Qx**.
7. Completar la visita.
8. Revisar la **QuickView de visita actual** antes de copiar el informe.
9. Copiar informe.
10. Copiar fila Excel y pegarla en `BD_VISITAS_HS`.
11. Pulsar **Nuevo paciente / limpiar formulario** antes de pasar al siguiente paciente.

> [!IMPORTANT]
> El `codigo_hs` se reserva durante la sesión, pero solo queda oficialmente registrado cuando se copia la fila Excel y se pega en `BD_VISITAS_HS`.

---

## Código HS

Formato obligatorio:

```text
HS0001
HS0002
HS0003
```

Reglas principales:

- se reutiliza si el paciente ya existe;
- se genera automáticamente si se inicia/selecciona un paciente sin código;
- no se genera mientras solo se escribe en el buscador;
- no se reciclan códigos generados durante la sesión;
- no se permite reasignar un código ya asociado a otro paciente;
- se bloquea la exportación si existe código HS sin NUHSA asociado.

---

## Archivos principales

| Ruta | Descripción |
|---|---|
| `index.html` | Herramienta web principal. |
| `vendor/xlsx.full.min.js` | SheetJS local para leer archivos Excel. |
| `templates/BD_VISITAS_HS_template.xlsx` | Plantilla vacía para uso real. |
| `templates/BD_VISITAS_HS_sintetico.xlsx` | Base sintética para formación y pruebas. |
| `scripts/generate_excel_template.py` | Regenera la plantilla Excel. |
| `scripts/generate_synthetic_data.py` | Regenera datos sintéticos. |
| `docs/MANUAL_USUARIO_ENFERMERIA_HS.md` | Manual de usuario detallado. |
| `docs/CODIGO_HS.md` | Reglas del código HS. |
| `docs/UX_FLUJO_CONSULTA.md` | Flujo recomendado de consulta. |
| `docs/DICCIONARIO_VARIABLES.md` | Diccionario de variables. |
| `docs/IMPORTACION_EXCEL.md` | Documentación de carga Excel. |
| `docs/QA_FASE_4_CODIGO_HS.md` | Checklist QA de código HS. |
| `docs/PLAN.md` | Plan y trazabilidad de fases. |

---

## Uso local

Abrir directamente:

```text
index.html
```

O servir localmente:

```bash
python -m http.server 8000
```

Después abrir:

```text
http://localhost:8000
```

---

## Bases Excel

| Archivo | Uso |
|---|---|
| `BD_VISITAS_HS_template.xlsx` | Plantilla vacía para comenzar con datos reales. |
| `BD_VISITAS_HS_sintetico.xlsx` | Archivo de práctica con pacientes ficticios. |

> [!WARNING]
> No mezclar datos sintéticos con datos reales.

---

## Seguridad y límites

- La herramienta no tiene backend.
- No usa `localStorage` para datos clínicos.
- Usa `sessionStorage` para mantener datos solo durante la sesión.
- No sustituye la historia clínica oficial.
- No sustituye la valoración profesional.
- La custodia de datos reales debe seguir las normas del equipo responsable.

---

## QA rápido antes de consulta

- [ ] Cargar base sintética.
- [ ] Buscar `TEST0001` y comprobar `HS0001`.
- [ ] Buscar `hs1` y comprobar normalización a `HS0001`.
- [ ] Buscar `TEST9998` y comprobar autogeneración de `HS0007`.
- [ ] Pulsar **Nuevo paciente / limpiar formulario** y comprobar que mantiene la BD cargada.
- [ ] Buscar `TEST9997` y comprobar autogeneración de `HS0008`.
- [ ] Probar PV, SG y CX.
- [ ] Copiar informe.
- [ ] Copiar fila Excel.
- [ ] Confirmar que no se exporta código HS sin NUHSA.
- [ ] Cargar plantilla vacía válida (sin errores en consola).
- [ ] Cargar base sintética completa.
- [ ] Comprobar **QuickView de visita actual** en PV/SG/CX.
- [ ] Comprobar botón **Ver resumen longitudinal** en la barra lateral.
- [ ] Comprobar que el panel muestra NUHSA y Código HS correctos.
- [ ] Comprobar que el informe TXT está ordenado por bloques.

---

## Fases completadas

<details>
<summary>Ver histórico funcional</summary>

| Fase | Resultado |
|---|---|
| Fase 1 | Adaptación clínica inicial del formulario. |
| Fase 2 | Exportación longitudinal única `BD_VISITAS_HS`. |
| Fase 2.1 | Corrección de brotes y número de médicos previos. |
| Fase 2.2 | Plantilla Excel reproducible. |
| Fase 2.3 | Cohorte sintética longitudinal. |
| Fase 3 | Importación Excel maestro y precarga temporal. |
| Fase 3.1 | Visibilidad de carga y búsqueda. |
| Fase 3.2 | QA documental de importación. |
| Fase 3.3 | UX global, EVAs rediseñadas y semáforos. |
| Fase 4 | Código HS manual seguro. |
| Fase 4.1 | Corrección de estado paciente/código HS. |
| Fase 4.2 | Búsqueda robusta por HS abreviado y exportación segura. |
| Fase 4.3 | Autogeneración segura de código HS. |
| Fase 6.5 | Informes TXT ordenados por bloques. |
| Fase 7A1 | QuickViews de visita actual (PV/SG/CX). |
| Fase 7A2 | QuickView longitudinal de paciente desde BD cargada. |

</details>

---

## Pendientes previstos

- Microsoft Forms institucional para PROMs remotos.
- QR/tarjetas para pacientes.
- Importación de respuestas remotas vinculadas por `codigo_hs`.
- Dashboard para seguimiento y explotación agregada.
- Refactor modular de HTML/CSS/JS cuando el flujo funcional esté estable.

---

## Documentación recomendada

- [Manual de usuario](docs/MANUAL_USUARIO_ENFERMERIA_HS.md)
- [Código HS](docs/CODIGO_HS.md)
- [Flujo de consulta](docs/UX_FLUJO_CONSULTA.md)
- [Importación Excel](docs/IMPORTACION_EXCEL.md)
- [Diccionario de variables](docs/DICCIONARIO_VARIABLES.md)
- [Plan del proyecto](docs/PLAN.md)

---

## Nota de beta

Durante la primera beta se recomienda probar con pocos pacientes y anotar incidencias concretas: campos que sobran, campos que faltan, dudas de uso, problemas de copiado, problemas al pegar en Excel o dificultad para cambiar entre pacientes.

El objetivo de la beta es comprobar si la herramienta encaja en el flujo real de consulta y qué ajustes hacen falta antes de escalar su uso.
