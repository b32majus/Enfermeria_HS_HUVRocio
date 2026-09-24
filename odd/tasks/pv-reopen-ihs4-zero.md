# Feature: pv-reopen-ihs4-zero (work order 2026-09-24)

Status: COMPLETED (2026-09-24; corrección posterior de integridad en `pv-reopen-safety-correction.md`)
Authority: docs/work-orders/2026-09-24-pv-reopen-ihs4-zero-audit.md (EXECUTION_READY)
Branch: fix/pv-reopen-ihs4-zero-20260924

## Constraints (from work order + AGENTS.md)

- No Excel schema changes; no data migration; no backend; no localStorage for clinical data.
- No invented PROM item responses; no invented regional lesion detail.
- Preserve SG/CX, patient identity, codigo_hs semantics.
- No push / PR / merge. Minimal blast radius on monolithic index.html.
- Unit 3 is REPORT-ONLY: create docs/audits/AUDIT_ENFERMERIA_HS_20260924.md, do not fix its findings.

## Tasks

1. [x] Reconcile git state (clean tree, branch correct, 1 commit ahead of origin/main) — done 2026-09-24.
2. [x] Explore index.html + docs; map Unit 1/2 target paths — done 2026-09-24.
3. [ ] UNIT 1: reopen existing PV for selected patient (button, deterministic latest-PV choice,
       restore stored fields only, keep stored date, no invented detail + discrete note,
       reexport = update/replace indication, no interference with SG/CX, no new codigo_hs).
       Close with work-unit commit.
4. [ ] UNIT 2: IHS4=0 valid value — fix longitudinal QuickView last/prev loop (`v === '0'` skip),
       SG change computations gated on `prev > 0` (SG alert, SG report, SG QuickView).
       Dashboard parseDashboardIhs4 already treats 0 correctly. Close with work-unit commit.
5. [ ] Deterministic regression checks (node-based extraction tests) for 8->0, 0->0, PV reopen mapping.
6. [ ] UNIT 3: repository-wide read-only audit -> docs/audits/AUDIT_ENFERMERIA_HS_20260924.md
       (delegate read-only scout; findings report-only).
7. [ ] Update only materially obsolete docs (README, MANUAL_USUARIO, PLAN, IMPORTACION_EXCEL if applies).
8. [ ] Native review lifecycle (RDD on): inspect -> start -> collect -> acknowledge. No delivery actions.
9. [ ] Final factual report; confirm clean worktree.

## Design decisions recorded

- Multiple PV rows: choose the most recent PV by fecha_visita (records already sorted ascending in
  getPatientRecords; Array.sort stability gives deterministic tie-break by import order). This mirrors
  getLastPatientRecord semantics and is clinically safe. Documented in manual. No product STOP needed.
- PROMs: restore stored totals only into readonly display fields (pv_dlqi_display, pv_hads_a/d_display,
  pv_hsqol_display, pv_hsqol_interp); never rebuild item accordions. Show a discrete note that item
  detail is not stored.
- IHS4 regional counters: restore only totals (pv_tot_n/a/f), zonas_activas_n/listado display; do not
  reconstruct pv_ihsData regions; show discrete note that regional distribution is not stored.
- PV reopen visibility: sidebar button (like "Ver resumen longitudinal"), shown when the selected
  patient has >=1 PV row in the loaded BD; updated from setSelectedPatientFromRecord flow.

## Evidence (commits)

- b76e873 — fix(pv): reabre primera visita guardada desde base cargada (index.html, 320 insertions, 0 deletions)
- 0df164f — fix(ihs4): trata IHS4=0 como valor clinico valido en rutas longitudinales (index.html, 29+/12-;
  incluye correctivo del BLOCKER B1 de verificación: sg_ihs4_previo vacío no se trata como 0, alerta oculta)
- a88bf45 — docs: documenta reapertura de PV guardada y tratamiento de IHS4=0 (4 archivos)

## Verification evidence

- node --check del script inline: OK en cada commit.
- Harness determinista (extracción de funciones reales + stubs): 6/6 PASS alerta SG en vivo
  (previo vacío -> oculta; 0->0 Estable (0); 0->2 (+2 puntos); 8->0 Mejoría (100%); 8->8 Estable (8);
  previo -1 -> oculta); batería previa del verificador: 26/28 PASS, 2 FAIL resueltos por el correctivo B1.
- gentle-ai-verify (read-only): Unidad 1 estática PASS, regresión PASS (solo index.html, SG/CX intactos),
  sin duplicados de funciones, MASTER_COLUMNS intacto, sin localStorage/fetch.
- Halazgos menores aceptados: M1 (política multi-PV documentada en manual/PLAN), M2 (display PROM vacío
  si el total no está almacenado — coherente con 'no inventar'), M3 (fecha no parseable -> input date vacío,
  nunca fecha actual).

## Remaining

- Ninguno. UNIT 3 audit committed (894a488); native review lineage review-a40128a6a089ddaf
  closed `approved` and acknowledged (authority burned, gentle-ai.review-acknowledged/v1).
  Advisory non-blocking finding R3-001 (reliability, index.html:6030, informational) left as
  later work per the closure advisory. Delivery untouched (no push/PR/merge per work order).
- Post-review: la revisión independiente detectó un defecto de integridad
  agregado/detalle en la reapertura histórica (recalculo/sobrescritura de totales y
  "Sin zonas activas" incorrecto). Corregido en el train correctivo
  `odd/tasks/pv-reopen-safety-correction.md` (commits 163d6a2, d059423, 5ca9826).
