# 🛠️ Procedure Occurrence Table

The `procedure_occurrence` table captures **medical procedures performed on or ordered for a patient**. This includes surgeries, imaging studies, vaccinations, and other interventions recorded as structured procedure codes.

In EHR systems like Epic or Cerner, this data typically comes from **procedure orders**, **surgical case records**, **radiology modules**, or **CPT/HCPCS-coded billing entries**. In claims data, it aligns with standard procedure lines.

Each row represents **a single instance of a procedure**, whether diagnostic or therapeutic, performed in outpatient or inpatient settings.

## Key OMOP Fields Mapped to Familiar EHR Concepts

| OMOP Field | EHR Analogy | Description | Clinical Relevance |
|------------|-------------|-------------|---------------------|
| `procedure_occurrence_id` | Procedure record ID | Unique identifier for the procedure event. | Used internally for joins. |
| `person_id` | Patient ID | Foreign key to `person`. | Ties the procedure to the patient. |
| `procedure_concept_id` | CPT / HCPCS / ICD → SNOMED | Standard concept for the procedure performed (e.g., "Colonoscopy", "CABG surgery"). | Use this for standardized analytics and cohort inclusion. |
| `procedure_date` | Date of procedure | When the procedure occurred. Time stored in `procedure_datetime`. | Critical for temporal anchors, sequence analysis. |
| `procedure_type_concept_id` | Source of data | Indicates whether the data came from EHR, billing, registry, etc. | Helpful for understanding source context. |
| `modifier_concept_id` | Procedure modifiers | Captures CPT modifiers or other procedure refinements (e.g., laterality, approach). | Important in surgical or detailed coding contexts. |
| `quantity` | Repeats or number performed | How many times the procedure was performed in that instance. | Relevant for vaccines, lab panels, etc. |
| `provider_id` | Performing provider | Clinician who performed or ordered the procedure. | Used for provider profiling and attribution. |
| `visit_occurrence_id` | Associated visit | Visit during which the procedure was performed. | Contextualizes setting and supports LOS analysis. |
| `procedure_source_value` | Original code | Raw procedure code from EHR/claims (e.g., CPT 45378). | Useful for auditing mappings or debugging pipeline. |
| `procedure_source_concept_id` | Source code’s mapped concept | Concept ID mapped from the raw code. | Occasionally used in QA workflows. |

## Common Pitfalls and What to Watch For

- **Overlap with `observation` and `measurement`**: Imaging (e.g., MRI) may show up in multiple domains depending on capture method. Confirm context before analysis.
- **Procedure frequency logic**: One row = one procedure event. Don’t assume it's one row per visit.
- **Modifiers often missing**: Many EHRs and claims systems do not populate `modifier_concept_id` fully.

## Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| How many patients received colonoscopy in the past 5 years? | `procedure_concept_id` = colonoscopy + `procedure_date` filter |
| What percentage of breast cancer patients had mastectomy vs lumpectomy? | `condition_occurrence` (breast cancer) + `procedure_occurrence` |
| Are cardiac catheterizations more likely to be followed by CABG? | `procedure_occurrence` with sequencing by `procedure_date` |
| How many patients received joint replacements at our surgical center? | `procedure_occurrence` + `care_site_id` or `visit_occurrence` |
| What’s the average time from prostate biopsy to definitive surgery? | `procedure_occurrence` filtered for biopsy and prostatectomy + date diff logic |