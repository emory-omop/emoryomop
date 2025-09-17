# 🧪 Measurement

The `measurement` table captures **structured numeric or categorical test results**—including laboratory values, vital signs, imaging reads, and other clinical assessments. These are typically values that have a clear result, unit, and reference range.

This maps to **lab results tables in Epic Clarity**, **flowsheet values**, and some **observation tables in Epic or CDW** depending on how results are recorded. In claims-based systems, this may be sparse. In EHR-derived OMOP CDMs, it’s a primary source for labs and vitals.

Each row represents **a single measurement event**, such as a blood pressure reading, creatinine level, or hemoglobin A1c result.

## Key OMOP Fields Mapped to Familiar EHR Concepts

| OMOP Field | EHR Analogy | Description | Clinical Relevance |
|------------|-------------|-------------|---------------------|
| `measurement_id` | Result ID | Unique identifier for the measurement. | Used internally for joins. |
| `person_id` | Patient ID | Foreign key to `person`. | Ties the result to the patient. |
| `measurement_concept_id` | LOINC or test concept | Standardized concept representing the test (e.g., "Hemoglobin A1c", "Systolic BP"). | Use for consistent test identification across systems. |
| `measurement_date` | Result date | Date the measurement was taken. Time available in `measurement_datetime`. | Used for longitudinal tracking, trend analysis. |
| `measurement_type_concept_id` | Source context | Indicates where the result came from (EHR, lab feed, patient-reported). | Helpful for interpreting reliability and source differences. |
| `value_as_number` | Numeric result | The numeric test value (e.g., 6.2 for A1c). | Central for lab-based cohort definitions or analytics. |
| `value_as_concept_id` | Categorical result | Concept ID for categorical results (e.g., "Positive", "Negative"). | Used when results are recorded as words rather than numbers. |
| `unit_concept_id` | Unit of measurement | Standardized unit concept (e.g., "mg/dL", "mmHg"). | Necessary for comparing values across systems. |
| `range_low`, `range_high` | Reference range | Normal value ranges for the test. | Useful for flagging abnormal results or standardizing thresholds. |
| `provider_id` | Ordering provider | Who ordered or recorded the result. | Useful for quality or attribution analyses. |
| `visit_occurrence_id` | Encounter ID | Visit associated with the measurement. | Helps contextualize inpatient vs outpatient settings. |
| `measurement_source_value` | Local test name | Original value as stored in the source system. | Helpful for mapping validation and QA. |
| `unit_source_value` | Local unit | Original unit as recorded in the EHR. | May differ slightly from standard mapping. |

## Common Pitfalls and What to Watch For

- **Lab and vital signs use the same table**: Blood pressure and hemoglobin live together—filter using `measurement_concept_id`.
- **Be cautious of units**: Not all sites standardize units perfectly—check mappings or use `unit_concept_id` consistently.
- **Categorical results**: Use `value_as_concept_id` for things like “Detected” / “Not Detected” tests.

## Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| What is the average A1c level among patients with diabetes? | `condition_occurrence` (diabetes) + `measurement_concept_id` = A1c |
| How many patients had low hemoglobin prior to surgery? | `measurement_concept_id` = Hgb + value threshold + `procedure_occurrence` |
| What’s the trend in creatinine over time in CKD patients? | `measurement_concept_id` = creatinine + `condition_occurrence` (CKD) |
| Which patients had a positive COVID-19 PCR test in the ER? | `measurement.value_as_concept_id` = Positive + `visit_occurrence` filtered for ER |
| What proportion of patients with hypertension have uncontrolled systolic BP? | `condition_occurrence` + `measurement_concept_id` for systolic BP + value filter |