---
search:
  exclude: false
hide:
#   - toc
  - footer
---

## 🧾 Visit Detail

The `visit_detail` table captures **subcomponents of a broader visit**. If the `visit_occurrence` table is the macro view (e.g., a full hospital stay), `visit_detail` is the micro view (e.g., a transfer from ICU to step-down, or from surgery to recovery).

This level of detail is particularly relevant in **inpatient settings**, where patients move between departments or beds, and each transition may represent a different level of care, provider team, or clinical focus. In Epic, this could map to **hospital accounts, ADT segments, or department changes**.

In systems where this level of granularity isn’t available, `visit_detail` may be sparse or even empty. But when used, it allows for fine-grained, time-aware analyses.

## Key OMOP Fields Mapped to Familiar EHR Concepts

| OMOP Field | EHR Analogy | Description | Clinical Relevance |
|------------|-------------|-------------|---------------------|
| `visit_detail_id` | Sub-encounter ID | Unique ID for each sub-visit. | Used to link fine-grained movements within a visit. |
| `person_id` | Patient ID | Foreign key to `person`. | Ties each visit detail to a specific patient. |
| `visit_occurrence_id` | Parent Visit ID | Links to the parent record in `visit_occurrence`. | Lets you roll up sub-visits into a full encounter. |
| `visit_detail_concept_id` | Level of care or room type | Standard concept for sub-visit type (e.g., ICU, Med/Surg, ER holding). | Allows classification of intra-visit transitions. |
| `visit_detail_start_date`, `visit_detail_end_date` | Start/stop of unit stay | Sub-visit time span. Captures movement through care settings. | Enables LOS by department or level-of-care transitions. |
| `provider_id` | Attending or unit-specific provider | Provider associated with this unit or sub-visit. | Helpful for provider attribution in team-based care. |
| `care_site_id` | Department or physical location | Location for this detail segment. | Can help map out service lines or unit-specific utilization. |
| `admitting_source_concept_id` | Source for this detail | Where the patient came from before this sub-visit. | Critical in step-wise trajectory analyses. |
| `discharge_to_concept_id` | Next location after this segment | Where the patient went after this sub-visit. | Useful for modeling inpatient workflows or step-down logic. |
| `preceding_visit_detail_id` | Prior segment | References the prior `visit_detail` record (if applicable). | Useful for reconstructing flow-of-care paths. |

## Common Pitfalls and What to Watch For

- **Not populated in many EHR extracts**: Only systems with robust ADT or departmental transfer logs populate this table. Check population stats before relying on it.
- **`visit_occurrence` ≠ `visit_detail`**: Don’t mix the two—use `visit_detail` when you need intra-visit resolution.
- **Time overlap edge cases**: Transfers may overlap slightly or have clock artifacts (e.g., midnight splits). Sanity check for continuous flow.

## Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| How many ICU stays were part of cancer-related hospitalizations? | `visit_detail.visit_detail_concept_id` + `visit_occurrence_id` joined to `condition_occurrence` |
| What’s the average length of time patients spend in the ER before being admitted? | `visit_detail` filtered by ER + `visit_occurrence` for admission |
| Which departments have the highest 30-day readmission rates? | `visit_detail.care_site_id` + readmission logic using `visit_occurrence` |
| Can we map inpatient journeys across units for a given cohort? | `visit_detail` ordered by `start_date` and `preceding_visit_detail_id` |
| How many patients were transferred to a step-down unit after surgery? | `visit_detail` + `procedure_occurrence` + concept filters for surgery and step-down |