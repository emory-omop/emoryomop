---
search:
  exclude: false
hide:
#   - toc
  - footer
---

#🧍 Person

The `person` table in OMOP CDM represents a **single row per patient**—think of it as the OMOP equivalent of the patient header in Epic or Cerner. However, unlike EHR systems where demographic details may be split across multiple modules or updated with each encounter, the OMOP `person` table **captures one consolidated record per individual**.

This table is foundational: most other OMOP tables (conditions, drugs, visits, etc.) link back to `person_id`.

## Key OMOP Fields Mapped to Familiar EHR Concepts

| OMOP Field | EHR Analogy | Description | Clinical Relevance |
|------------|-------------|-------------|---------------------|
| `person_id` | MRN or Enterprise ID | OMOP’s internal unique ID for each patient. Often derived from source MRN or unique enterprise ID but re-identified for privacy. | Needed to join across all patient-level data. |
| `gender_concept_id` | Sex at birth / Gender Identity | Links to a standard concept like "Male", "Female", etc. May differ from `gender_source_value` (what’s recorded in EHR). | Important for sex-based cohort definitions. |
| `year_of_birth` + `month_of_birth` + `day_of_birth` | DOB | Captured as separate fields (can support date shifting for de-identification). Full DOB used only if permitted. | Age-based eligibility, pediatric vs adult cohorts. |
| `race_concept_id`, `ethnicity_concept_id` | Race/Ethnicity fields in registration | Standardized values to support cross-site analytics. Mapped from source values. | Useful in health equity research or SDoH analyses. |
| `location_id` | Home address or zip | Points to `location` table (not in `person` directly). Often de-identified. | Can be used for geospatial or regional analyses. |
| `provider_id` | PCP or Managing Physician | Links to primary provider if available. May be null in data where provider not consistently recorded. | Could anchor attribution or provider-level studies. |
| `care_site_id` | Facility or department | Links the patient to their main care site. In multi-hospital systems, helps attribute patients to locations. | Useful for site-level variation or resource analysis. |
| `*_source_value` | Raw EHR entry | Reflects the original text value from the EHR system (e.g., "M", "F", "Hispanic or Latino"). | Can help debug mapping inconsistencies or local terminologies. |

## Common Pitfalls and What to Watch For

- **No longitudinal demographic tracking**: This table is a snapshot—not a history. Updates to gender, race, or location over time are not captured here. You’ll need to look at `observation` or other domains if longitudinal info is critical.
- **Birthdates may be shifted or truncated**: Especially in de-identified datasets, full birthdates may be redacted to protect privacy.
- **Source values ≠ standard values**: Always use the `*_concept_id` fields for analytics to ensure standardization across sites.

## Clinical Use Cases

Here are some examples of where you'd use this information. It relies upon both the above table, as well as contains a requirement for another table to help guide your search for additional information.

| Question | Where to Look |
|----------|----------------|
| What is the proportion of female african american patients in the database? | `person.race_concept_id` + `person.gender_concept_id` |
| What is the average age of patients (frozen at death date) in the database? | `person.year_of_birth` + `death` | 
| Are there disparities in statin prescribing across racial groups? | `person.race_concept_id` + `drug_exposure` |
| How many pediatric patients were seen for asthma in the past year? | `person.year_of_birth` + `condition_occurrence` |
| What are the characteristics of patients seen at our cancer center? | `person.care_site_id` + `visit_occurrence` |