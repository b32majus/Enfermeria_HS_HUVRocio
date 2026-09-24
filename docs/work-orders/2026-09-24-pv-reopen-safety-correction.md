# Corrective Work Order — Safe historical PV reopen

Status: EXECUTION_READY
Date: 2026-09-24

## Context

Independent post-review audit found a data-integrity defect in the recently
implemented reopening of an existing Primera Visita.

The Excel schema intentionally does not store:
- per-region IHS4 N/A/F distribution;
- individual DLQI answers;
- individual HADS answers;
- individual HSQoL-24 answers.

The current reopened-PV implementation correctly restores stored aggregate
scores but leaves the non-persisted detailed UI empty and interactive.

That creates a risk of recalculating historical aggregate values from incomplete
detail and of exporting/reporting false information.

## Unit 1 — Preserve historical IHS4 authority

When a PV is reopened from the loaded database:

- stored ihs4_actual remains authoritative;
- stored nodulos_total / abscesos_total / fistulas_total remain authoritative;
- stored zonas_activas_n / zonas_activas_listado remain authoritative;
- do not reconstruct regional N/A/F distribution;
- do not report "Sin zonas activas" merely because pv_ihsData cannot be reconstructed;
- QuickView must use the stored zone summary in historical-PV mode;
- copyReport_pv must use the stored zone summary in historical-PV mode and state
  discreetly that regional N/A/F detail was not stored;
- exportData_pv must preserve stored zonas_activas_n and
  zonas_activas_listado in historical-PV mode;
- regional +/- controls must not be able to silently recalculate the historical
  aggregate IHS4 from an empty internal map.

Do not change the Excel schema.

Do not invent regional distribution.

Do not add a new "recalculate IHS4" workflow in this correction.

## Unit 2 — Preserve historical PROM authority

When a PV is reopened:

- restore DLQI/HADS/HSQoL aggregate values exactly as stored;
- do not reconstruct individual questionnaire responses;
- individual questionnaire controls must not be able to recalculate/overwrite
  the stored historical total from a partial new set of answers;
- prefer disabling the unavailable detailed item controls in historical-PV mode,
  with a discreet explanation that item-level responses were not stored;
- do not add new Excel fields or questionnaire state.

## Unit 3 — Lifecycle and reset safety

Historical-PV mode must be cleared when:
- changing patient;
- starting a new patient;
- clearing forms;
- otherwise leaving the reopened historical PV context where appropriate.

Normal brand-new PV entry must retain the current fully interactive IHS4 and PROM
workflow.

SG and CX must remain unchanged.

## Unit 4 — Documentation / evidence

Update the new PV documentation so it does not claim that unavailable detailed
data can be continued as if reconstructed.

Document that stored aggregate values remain authoritative when reopening.

Add a short post-review addendum to:
docs/audits/AUDIT_ENFERMERIA_HS_20260924.md

Record:
- the reopened-PV aggregate/detail integrity defect found independently;
- that it is fixed by this corrective work unit;
- the separately observed dashboard yes/no/current-vs-historical findings as
  deferred follow-up, without fixing them in this train.

Correct the ODD task status/checklist so it reflects the actual completed state.

Ensure git diff --check is clean for newly touched lines.

## Verification

Deterministically verify at least:

1. Reopen stored PV:
   IHS4 8, N=4, A=2, F=0, zonas=3/"Axilas(D), Ingles(I), Glúteo(D)"
   -> UI aggregate values remain 8 / 4 / 2 / 0 / 3 / stored zone list.

2. Without changing anything:
   -> QuickView does NOT say "Sin zonas activas".
   -> report does NOT say "Sin zonas activas".
   -> exported row preserves zonas_activas_n=3 and stored zone list.

3. Regional controls cannot silently convert the historical aggregate to a
   score derived from an empty map.

4. Stored DLQI/HADS/HSQoL totals survive reopening and cannot be replaced by
   clicking one isolated questionnaire item.

5. Start a new PV:
   -> normal regional counters and questionnaires remain fully interactive.

6. IHS4 regression:
   -> 8 -> 0 remains Mejoría.
   -> 0 -> 0 remains Estable.
   -> empty previous remains not evaluable.

7. node syntax check.
8. git diff --check.
9. no Excel schema change.
10. no localStorage/backend/external telemetry.

## Boundaries

No dashboard fixes in this train.
No general audit-debt fixes.
No refactor.
No schema migration.
No push.
No PR.
No merge.
