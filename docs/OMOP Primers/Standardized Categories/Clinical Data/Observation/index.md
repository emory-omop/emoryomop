# 🚬 Observation

The `observation` table is a **catch-all for clinical facts about a patient that don’t fit into other domains** like condition, drug, or measurement. This includes data such as social determinants of health, smoking status, patient-reported outcomes, screening responses, and sometimes free-text concepts coded into structured fields. The general rule of thumb for the OHDSI community is if you can possible represent the data in a different table, do so. If not, use this table.

In Epic or Cerner, this may map to **flowsheet entries, social history tabs, questionnaires, or structured note templates**. It is often the home for **important non-diagnostic data** such as advance directives, functional status, or reasons for medication non-use.

Use this table when you're trying to capture **qualitative or coded observations** that inform care but are not classic diagnoses or measurements.

## Key OMOP Fields Mapped to Familiar EHR Concepts

| OMOP Field | EHR Analogy | Description | Clinical Relevance |
|------------|-------------|-------------|---------------------|
| `observation_id` | Observation record ID | Unique identifier for the observation. | Used internally for joins. |
| `person_id` | Patient ID | Foreign key to `person`. | Ties the observation to the patient. |
| `observation_concept_id` | Structured concept | Standardized concept for the observation (e.g., "Former smoker", "Lives alone"). | Use this for cohort logic or population segmentation. |
| `observation_date` | Observation date | When the observation occurred or was recorded. Time stored in `observation_datetime`. | Important for anchoring to clinical timeline. |
| `observation_type_concept_id` | Source type | Indicates origin of observation (EHR, survey, registry, etc.). | Useful in understanding reliability or mode of capture. |
| `value_as_number` | Quantitative value | Numeric value, if applicable (e.g., pack years smoked). | Used when the observation includes a scale or score. |
| `value_as_string` | Free-text value | Text value, if recorded that way. | Can be used for manual audits, not for standardized analytics. |
| `value_as_concept_id` | Coded value | Concept ID when response is categorical (e.g., "Yes", "No", "Former"). | Preferred for categorical analytics. |
| `qualifier_concept_id` | Modifier or context | Optional extra context (e.g., severity or status). | Occasionally populated for nuanced responses. |
| `unit_concept_id` | Units of numeric values | Unit concept if the observation is quantitative. | Important for interpreting `value_as_number`. |
| `provider_id` | Recording clinician | Who recorded the observation. | Useful for attribution or audit. |
| `visit_occurrence_id` | Visit ID | Visit during which the observation occurred. | Contextualizes the setting (e.g., inpatient vs outpatient). |
| `observation_source_value` | Local field name or label | Original value as stored in EHR (e.g., "TOB_STATUS"). | Useful for mapping validation or source tracing. |

## Common Pitfalls and What to Watch For

- **Overlap with `condition_occurrence` and `measurement`**: Use `observation` only when the data doesn’t clearly belong in those tables.
- **Text vs concept**: Try to use `value_as_concept_id` for standardization. `value_as_string` is rarely analyzable directly.
- **High site-specific variation**: Different institutions load different types of observations—be aware of what your dataset includes.

## Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| What percentage of patients are current smokers? | `observation_concept_id` = smoking status + `value_as_concept_id` |
| How many patients screened positive for food insecurity? | `observation_concept_id` for SDOH + `value_as_concept_id` = positive |
| What’s the prevalence of patient-reported pain scores > 6? | `observation_concept_id` = pain score + `value_as_number` filter |
| Which patients declined statin therapy despite indication? | `observation` for medication non-use + `condition_occurrence` |
| What proportion of hospice patients have an advance directive recorded? | `observation_concept_id` = advance directive + `condition_occurrence` or `visit_occurrence` |
