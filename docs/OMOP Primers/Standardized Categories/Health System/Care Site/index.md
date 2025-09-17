# 🏢 Care Site

The `care_site` table represents **physical locations or organizational units** within a healthcare system where care is delivered. This can include **hospitals, outpatient clinics, departments, specialty units, or service lines**.

In Epic or Cerner, this typically maps to **facility or department IDs**, **clinic names**, or **cost center groupings** used in scheduling and encounter metadata. In OMOP, the `care_site_id` is referenced by other tables—like `visit_occurrence`, `person`, and `provider`—to indicate *where* care was delivered or attributed.

### Key OMOP Fields Mapped to Familiar EHR Concepts

| OMOP Field | EHR Analogy | Description | Clinical Relevance |
|------------|-------------|-------------|---------------------|
| `care_site_id` | Department or facility ID | Unique ID for the care site. | Used for facility- or department-level stratification. |
| `care_site_name` | Clinic name / Facility name | Human-readable name of the care site (e.g., "Emory Winship Cancer Center"). | Useful for labeling or QA. |
| `place_of_service_concept_id` | Setting type | Standard concept for place of care (e.g., "Inpatient hospital", "Outpatient clinic"). | Enables classification of care context across systems. |
| `location_id` | Address link | Foreign key to the `location` table with physical address info. | Supports regional or spatial analysis. |
| `care_site_source_value` | Local care site code | Original identifier from the source system (e.g., CLARITY_DEPARTMENT_ID). | Useful for mapping, QA, or linking to non-OMOP data. |
| `place_of_service_source_value` | Raw POS string | Local text for place of service, if available (e.g., "ED", "Ambulatory"). | Helps validate POS mappings or site classifications. |

### Common Pitfalls and What to Watch For

- **Granularity varies by site**: Some sites use departments as care sites; others use whole facilities. Understand your data model.
- **Not all visits link to a `care_site_id`**: Especially true for data extracted from billing or claims. Validate completeness before using as a filter.
- **Use `location_id` for geospatial work**: `care_site` is the logical unit; `location` provides physical address/geography.

### Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| How many patients were seen at our main cancer center last year? | `visit_occurrence.care_site_id` + `care_site_name` |
| Are hypertension control rates different across our primary care clinics? | `measurement` + `visit_occurrence` + `care_site_id` |
| What is the procedure volume for each surgical department? | `procedure_occurrence` + `visit_occurrence.care_site_id` |
| Can we geocode our care delivery locations to study access? | `care_site.location_id` + `location.latitude/longitude` |
| Which care sites have the highest readmission rates? | `visit_occurrence.care_site_id` + readmission logic |