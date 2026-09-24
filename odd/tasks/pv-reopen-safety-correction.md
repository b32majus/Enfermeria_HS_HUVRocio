# Feature: pv-reopen-safety-correction (corrective work order 2026-09-24)

Status: COMPLETED (2026-09-24)
Authority: docs/work-orders/2026-09-24-pv-reopen-safety-correction.md (EXECUTION_READY)
Branch: fix/pv-reopen-ihs4-zero-20260924 (bounded corrective train, no push/PR/merge)

## Constraints

- Stored Excel aggregates are authority in reopened historical-PV mode.
- Never invent unavailable regional N/A/F or questionnaire item detail.
- Historical QuickView/report/export must preserve stored aggregate/zone data.
- Unavailable detailed controls must not silently recalculate historical scores.
- Normal new-PV workflow stays fully interactive; IHS4=0 behavior preserved.
- SG/CX unchanged; no Excel schema changes; no dashboard/audit-debt fixes; no refactor.
- No push / PR / merge.

## Tasks

1. [x] UNIT 1 — Historical IHS4 authority: zone-summary helper (historical-aware),
       QuickView stored zones, report stored zones + discreet note, export preserves
       stored zonas_activas_n/listado, updIHS_pv + calcTotalIHS_pv guarded in
       historical mode. Close with work-unit commit.
       — commit 163d6a2 (fix(pv): autoridad historica de IHS4 y zonas en PV reabierta).
2. [x] UNIT 2 — Historical PROM authority: DLQI/HADS/HSQoL calc guards in historical
       mode; disable unavailable item controls with discreet explanation. Close with
       work-unit commit.
       — commit d059423 (fix(pv): autoridad historica de PROMs y bloqueo de detalle no almacenado).
3. [x] UNIT 3 — Lifecycle/reset safety: lock cleared on resetPvFormForDbLoad,
       clearClinicalForms and patient-context reset; new PV fully interactive.
       Close with work-unit commit.
       — commit 5ca9826 (fix(pv): limpia modo historico PV al cambiar de paciente o reiniciar).
4. [x] Deterministic verification harness (node): reopen mapping, zone summary,
       export preservation, guards, new-PV interactivity, IHS4 8->0 / 0->0 regressions,
       node --check, git diff --check.
       — 36/36 PASS (/tmp/verify-pv-reopen-safety.js, extracción de funciones reales + stubs DOM);
       node --check OK; git diff --check limpio en líneas nuevas; sin cambios de esquema
       MASTER_COLUMNS; sin localStorage/fetch/telemetría añadidos.
5. [x] UNIT 4 — Documentation: MANUAL_USUARIO reopen section correction, PLAN/README
       alignment if materially obsolete, audit addendum (defect fixed + deferred
       dashboard findings), ODD task statuses (this file + pv-reopen-ihs4-zero).
       — MANUAL y PLAN corregidos; README sin afirmación obsoleta (sin cambios);
       adenda D-01/D-02 en docs/audits/AUDIT_ENFERMERIA_HS_20260924.md.
6. [x] Native Gentle review lifecycle (RDD on): inspect -> start -> collect ->
       acknowledge. No delivery actions.
       — lineage review-43dc84d3f5b9b521 (medium, lente review-reliability). Resultado:
       1 hallazgo CRITICAL R3-001 (reexportación de PV con totales IHS4 ausentes los
       registraba como ceros). Corrección acotada aplicada y validada: commit 1bbb4ad.
       Estado final approved; autoridad quemada (gentle-ai.review-acknowledged/v1).
       — Segunda generación (tras el commit de cierre ODD 8d4e38a): lineage
       review-149c342c31db5db0 (medium, review-reliability + refuter). Hallazgo CRITICAL
       R3-001 (inferencial, behavior-activated): el listado de zonas de la fila guardada
       pasaba al QuickView sin escapar. Corrección acotada aplicada y validada: commit
       f7c5b6f (escape con dashboardEscapeHtml). Estado final approved; autoridad quemada
       (gentle-ai.review-acknowledged/v1).
7. [x] Final factual report: commits, files changed, verification evidence, clean tree.

## Review outcome (evidence)

- R3-001 (CRITICAL, deterministic, introduced): restore solo sobrescribía totales/score
  cuando el valor almacenado era no vacío; el 0 del reinicio quedaba y la reexportación
  convertía mediciones históricas ausentes en ceros registrados.
- Corrección (commit 1bbb4ad, 6+/6-): en openExistingPv, totales N/A/F y score ausentes
  se muestran vacíos (export vacío), nunca ceros inventados.
- Validación dirigida del proveedor: PASS (estado approved); acknowledgement quemó la
  autoridad; delivery queda en política ordinaria del repositorio (sin push/PR/merge).
- Segundo ciclo: hallazgo R3-001 escape QuickView histórico corregido en f7c5b6f
  (3+/3-); validación dirigida PASS; approved + acknowledged (authority burned).

## Line-ending note

El monolito index.html tiene finales de línea mixtos en el repositorio. Las líneas
nuevas de este train se escribieron con LF para mantener `git diff --check` limpio
en las líneas tocadas; las líneas sin modificar conservan sus bytes originales
(diff neto: 104+/8-).
