# Corrective Work Order — Historical PV null preservation

Status: EXECUTION_READY
Date: 2026-09-24

## Problem

Independent final audit found one remaining round-trip integrity defect in
historical PV reopen.

The importer accepts older/partial rows and optional cells may legitimately be
empty or absent.

When such a PV is reopened, several controls reset to a negative default and a
subsequent export can convert historical "not recorded" into an explicit "No".

This must not happen.

## Unit 1 — Historical select fields

For a reopened historical PV, an empty stored value must remain empty unless the
user explicitly selects a value.

Affected fields currently having a non-empty first/default option include:

- antecedentes_familiares_hs
- fiebre_brotes
- biologico_previo
- deseos_genesicos
- tabaco_estado
- intentos_cesacion
- material_educativo_entregado
- impacto_sexual
- aislamiento_social

Required behavior:

- stored empty -> UI must not silently select "No", "No afectado", etc.;
- stored empty + no user edit -> export remains empty;
- stored empty + explicit user selection -> export selected value;
- stored non-empty -> restore and export normally.

Do not change the behavior of a brand-new PV unless strictly necessary.
No Excel schema changes.

## Unit 2 — Historical comorbidities

The 14 yes/no comorbidity columns must preserve the distinction between
missing/not-recorded and explicit No.

Historical export semantics:

- original empty/absent + inactive card -> preserve empty;
- original No + inactive -> No;
- original Si/Sí + active -> Si;
- original empty/No + user activates -> Si;
- original Si/Sí + user deactivates -> No.

Do not add columns or tri-state schema fields.

A small historical-aware export helper is preferred over changing the whole
comorbidity UI.

## Unit 3 — Missing historical active-zone display

When a reopened PV has no stored zonas_activas_n and no
zonas_activas_listado:

- do not visually display 0 / Ninguna as if assessed;
- show a neutral NR / No registrado representation;
- export must continue preserving the original empty values.

If stored count=0 is explicitly present, 0 is valid and may be represented as
no active zones.

## Unit 4 — Code hygiene within touched branch

- Remove duplicate object keys `ihs4_actual` and `gravedad_ihs4` in
  `exportData_pv`.
- Make `git diff --check origin/main...HEAD` clean, including the existing
  whitespace introduced by this branch.
- Preserve existing mixed-line-ending file content outside the necessary lines;
  do not normalize index.html wholesale.

## Regression requirements

Preserve all previously accepted behavior:

- historical IHS4 aggregate authority;
- historical PROM aggregate authority;
- historical regional/PROM detail lock;
- historical zones preserved;
- IHS4 8 -> 0 = Mejoría;
- IHS4 0 -> 0 = Estable;
- empty previous IHS4 = not evaluable;
- SG/CX unchanged;
- no Excel schema changes;
- no localStorage/backend/telemetry.

## Deterministic tests

Extend the current harness to cover at least:

1. historical blank select -> remains blank after reopen and reexport;
2. historical explicit No -> remains No;
3. historical blank select + explicit choice -> chosen value exports;
4. historical blank comorbidity + untouched -> empty;
5. historical No comorbidity + untouched -> No;
6. historical Sí comorbidity + untouched -> Si;
7. blank/No -> user activates -> Si;
8. Sí -> user deactivates -> No;
9. missing zonas count/list -> UI does not claim 0/Ninguna;
10. explicit zonas count 0 -> remains valid zero;
11. previous 36 historical-PV/IHS4 regressions remain PASS;
12. node --check PASS;
13. git diff --check origin/main...HEAD PASS.

## Delivery boundary

No dashboard fixes.
No general audit debt.
No broad refactor.
No schema changes.
No push.
No PR.
No merge.

Complete the native review cycle on the final mutation generation.
After acknowledgement, do not create another bookkeeping/documentation commit.
