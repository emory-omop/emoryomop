---
search:
  exclude: false
hide:
#   - toc
  - footer
---

# 🏥 Visit Occurrence

The `visit_occurrence` table captures each encounter a patient has with the healthcare system. This includes outpatient visits, inpatient admissions, ER encounters, and other settings.

Think of this as the **encounter table** in Epic or Cerner—where you’d see one row per visit with start and end times, type of visit (e.g., outpatient, inpatient), and associated providers and care sites. In OMOP, each row represents a distinct **episode of care**, and is used to group other events (conditions, procedures, measurements, etc.) to that visit.

This table is often critical for defining **temporal anchors**—cohort entry dates, episode windows, and care setting-based filters.

## Key OMOP Fields Mapped to Familiar EHR Concepts

| OMOP Field | EHR Analogy | Description | Clinical Relevance |
|------------|-------------|-------------|---------------------|
| `visit_occurrence_id` | Encounter ID | Unique identifier for each visit/encounter. | Used to join lab results, conditions, etc., to a specific visit. |
| `person_id` | Patient ID | Foreign key to the `person` table. | Links the visit to the patient. |
| `visit_concept_id` | Encounter Type | Standard concept for visit type (e.g., Inpatient, ER, Outpatient). | Filters events by setting (e.g., hospitalizations vs office visits). |
| `visit_start_date`, `visit_end_date` | Admit/Discharge or Check-in/Check-out | Date the visit started and ended. Time component in `*_datetime`. | Anchors for temporal logic (e.g., pre/post visit analysis). |
| `visit_type_concept_id` | Source of data | Indicates if the data was collected from EHR, claims, etc. | Useful for understanding provenance. |
| `provider_id` | Attending / Ordering Provider | Provider associated with the visit. | Useful for provider-level quality reporting. |
| `care_site_id` | Hospital or Clinic | Where the visit occurred (facility or department). | Enables site-level reporting or regional analyses. |
| `visit_source_value` | Raw Encounter Type | Original EHR value like "Inpatient" or "Office Visit". | Useful for troubleshooting mapping or auditing data fidelity. |
| `admitting_source_concept_id` | Admit Source | Concept for where the patient came from (e.g., home, SNF, ER). | Important for admission trend analysis. |
| `discharge_to_concept_id` | Discharge Disposition | Concept for where patient went after discharge (e.g., home, rehab). | Useful for readmission or discharge planning analyses. |

## Common Pitfalls and What to Watch For

- **Multiple rows per patient**: Unlike the `person` table, expect many rows per patient here—one for each encounter.
- **Missing or same-day end dates**: For outpatient visits, `visit_end_date` may match `visit_start_date`. Be cautious interpreting length of stay.
- **Not all data sources populate `provider_id` or `care_site_id` consistently**: This can affect provider- or site-level analyses unless cross-validated.

## Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| What is the average number of outpatient visits per patient per year? | `visit_occurrence.visit_concept_id`, `visit_start_date`, `person_id` |
| What are the readmission rates to the hospital within 30 days? | `visit_occurrence.visit_concept_id`, `visit_start_date`, `visit_end_date`, `person_id` |
| How often are patients discharged to skilled nursing facilities? | `discharge_to_concept_id` |
| What’s the typical length of stay for cancer patients? | `visit_occurrence` + `condition_occurrence` |
| How many ER visits occurred prior to hospitalization for stroke? | `visit_occurrence.visit_concept_id` + `condition_occurrence` (stroke concept) + temporal ordering by `visit_start_date` |
| What is the average time between outpatient visits and hospitalizations? | `visit_occurrence.visit_concept_id` + `visit_start_date` + `condition_occurrence` |