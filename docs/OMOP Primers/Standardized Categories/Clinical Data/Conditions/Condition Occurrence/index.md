# 🩺 Condition Occurrence

The `condition_occurrence` table captures **diagnoses or problems recorded for a patient**. These could be billing diagnoses, problem list entries, discharge diagnoses, or other medical conditions documented in structured EHR fields.

This is analogous to the **Diagnosis table in Epic Clarity**, or **Encounter Diagnoses** from a problem list or claims extract. Each row represents a **single recorded condition event**, often tied to a visit, but not always.

This table is frequently used in **cohort definitions**, **risk stratification**, and **prevalence analyses**.

## Key OMOP Fields Mapped to Familiar EHR Concepts

| OMOP Field | EHR Analogy | Description | Clinical Relevance |
|------------|-------------|-------------|---------------------|
| `condition_occurrence_id` | Condition record ID | Unique identifier for the condition event. | Used internally for joins. |
| `person_id` | Patient ID | Foreign key to `person`. | Ties the diagnosis to the patient. |
| `condition_concept_id` | ICD/CPT → SNOMED mapping | The standardized concept representing the condition (e.g., "Type 2 Diabetes Mellitus"). | Always use this field for standardized analytics. |
| `condition_start_date` | Date of diagnosis | When the condition began or was recorded. | Used for index date logic, onset timing, or longitudinal tracking. |
| `condition_end_date` | Resolution or stop date | End of condition (if known). Often null in EHRs. | Important for chronic vs resolved conditions. |
| `condition_type_concept_id` | Source/Context | Indicates origin of condition (e.g., problem list, billing claim, EHR note). | Helps disambiguate diagnoses entered for billing vs clinical use. |
| `condition_source_value` | ICD-10 / ICD-9 / local code | The source code as it appeared in the EHR. | Helpful for debugging mapping or tracing clinical coding. |
| `condition_source_concept_id` | Source code mapping | Maps the source value to its concept ID (if standardized). | May help trace source system logic. |
| `visit_occurrence_id` | Linked visit | Visit during which the diagnosis was recorded. May be null for ongoing problems. | Useful for anchoring to encounter context. |
| `provider_id` | Recording provider | Who recorded the diagnosis (if available). | Used in provider attribution or documentation audits. |

## Common Pitfalls and What to Watch For

- **Multiple entries for same diagnosis**: Chronic conditions may be re-recorded across multiple visits.
- **Missing `condition_end_date`**: Most EHRs don’t track when a condition resolves. Null ≠ still active.
- **Use `condition_concept_id` for analysis**: Avoid analyzing `condition_source_value` unless exploring site-specific logic.

## Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| How many patients were diagnosed with diabetes in the past year? | `condition_concept_id` + `condition_start_date` |
| What is the average time between cancer diagnosis and first treatment? | `condition_occurrence` (cancer concepts) + `drug_exposure` or `procedure_occurrence` |
| Which patients had a diagnosis of COVID-19 during their inpatient stay? | `condition_concept_id` + `visit_occurrence_id` + `visit_concept_id` |
| Are certain diagnoses more likely to occur during ER visits? | `condition_occurrence` + `visit_occurrence` with `visit_concept_id` = ER |
| What comorbidities are associated with patients admitted to the ICU? | `condition_occurrence` + `visit_detail` filtered for ICU |