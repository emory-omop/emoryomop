---
search:
  exclude: false
hide:
#   - toc
  - footer
---

# ⛔ Death

The `death` table captures **mortality information for a patient**, typically sourced from EHR discharge records, vital statistics registries, or external sources such as a state death index. This table **contains one row per deceased person**, if death is known or recorded. For example, Emory supplements EHR data with **state death data**.

This table is essential when defining **overall survival**, **mortality-related endpoints**, and **time-to-event** analyses.

## Key OMOP Fields Mapped to Familiar EHR Concepts

| OMOP Field | EHR Analogy | Description | Clinical Relevance |
|------------|-------------|-------------|---------------------|
| `person_id` | Patient ID | Foreign key to `person`. | Identifies the deceased individual. |
| `death_date` | Date of death | Date of death, as recorded. | Primary date used for censoring in survival analyses. |
| `death_datetime` | Time of death | If available, includes timestamp. | Rarely populated unless from inpatient EHR source. |
| `death_type_concept_id` | Source of record | Indicates where the death record came from (EHR, claims, registry). | Helps interpret data reliability. |
| `cause_concept_id` | Primary cause of death | Standard concept (often SNOMED) for cause of death, if available. | Useful for cause-specific mortality studies. |
| `cause_source_value` | Source code (e.g., ICD-10) | Original value for cause of death. | May be from death certificate or final discharge diagnosis. |
| `cause_source_concept_id` | Mapped cause concept | Standard concept ID mapped from the source code. | Supports analytics using OMOP vocabularies. |

## Common Pitfalls and What to Watch For

- **Absence ≠ alive**: If a person is not in the `death` table, it doesn’t guarantee they’re living—death may not have been captured.
- **Death date may be imprecise**: Especially when sourced from claims or external datasets. Validation is recommended for survival analyses.
- **Cause of death not always present**: Many EHRs do not record structured cause of death unless pulled from vital records.

## Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| What is the all-cause mortality rate in the oncology cohort? | `death.person_id` + `condition_occurrence` (cancer) |
| What’s the median time from diagnosis to death in Alzheimer’s patients? | `condition_occurrence` (Alzheimer's) + `death_date` |
| How many patients died within 30 days of a surgical procedure? | `procedure_occurrence` + `death` + date interval logic |
| What is the distribution of causes of death in stroke patients? | `condition_occurrence` (stroke) + `death.cause_concept_id` |
| Are mortality rates higher in patients with social risk factors (e.g., food insecurity)? | `observation` + `death` |