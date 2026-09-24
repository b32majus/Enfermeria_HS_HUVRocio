# Feature: pv-roundtrip-null-preservation (corrective work order 2026-09-24)

Status: COMPLETED (2026-09-24)
Authority: docs/work-orders/2026-09-24-pv-roundtrip-null-preservation.md (EXECUTION_READY)
Branch: fix/pv-reopen-ihs4-zero-20260924 (final bounded corrective train before delivery)

## Invariant

NOT RECORDED is not equivalent to NO. Historical empty values are preserved
unless the user explicitly changes the field.

## Tasks

1. [x] UNIT 1 — Historical select fields: helper `setPvHistoricalSelectFromRow`
       (stored empty -> no selection; export stays empty until explicit user choice)
       applied to the 9 non-empty-default selects in openExistingPv.
       — commit 0c5304c.
2. [x] UNIT 2 — Historical comorbidities: historical-aware export helper
       (empty+inactive -> empty; No+inactive -> No; Si+active -> Si;
       empty/No+active -> Si; Si+deactivated -> No). No schema changes.
       Map hoisted to shared PV_COMORB_COLUMNS. — commit 909c23e.
3. [x] UNIT 3 — Missing historical active-zone display: `getPvStoredZonesDisplay`
       helper; NR / No registrado when count and listado are absent; explicit 0
       remains valid zero; export keeps preserving stored empties. — commit 43f46cb.
4. [x] UNIT 4 — Hygiene: duplicate `ihs4_actual`/`gravedad_ihs4` keys removed in
       exportData_pv; 7 trailing-CR lines fixed and audit doc EOF blank line removed;
       `git diff --check origin/main...HEAD` clean; no wholesale line-ending
       normalization. — commit 99091e7.
5. [x] Deterministic harness extension: 55/55 PASS = 36 previous historical-PV/IHS4
       regressions + 19 new checks covering work-order tests 1-10 (selects, comorbidades,
       zonas display/export). node --check PASS.
6. [x] Native Gentle review lifecycle on the final candidate; after final
       acknowledgement: no further commits, no ODD/docs bookkeeping, no push/PR/merge.
       (The review receipt lives in the native store; per the delivery boundary this
       document is intentionally not modified after the final acknowledgement.)

## Evidence (commits)

- 0c5304c — fix(pv): preserva valores ausentes en selects historicos de PV
- 909c23e — fix(pv): preserva vacio vs No explicito en comorbilidades historicas
- 43f46cb — fix(pv): representacion neutra NR para zonas activas ausentes
- 99091e7 — chore: higiene en rama: claves duplicadas y espacios finales
