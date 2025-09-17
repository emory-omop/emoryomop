# 👩‍⚕️ Provider

The `provider` table represents **individual clinicians or care team members** who deliver or document healthcare services—such as physicians, residents, nurses, PAs, therapists, or advanced practice providers.

In Epic or Cerner, this typically maps to **provider master tables**, including **attending, ordering, performing, or authoring providers** found across encounters, procedures, prescriptions, and documentation.

This table allows OMOP to support **provider attribution**, **practice pattern analysis**, and **linkage of care events to clinicians**.

### Key OMOP Fields Mapped to Familiar EHR Concepts

| OMOP Field | EHR Analogy | Description | Clinical Relevance |
|------------|-------------|-------------|---------------------|
| `provider_id` | Provider ID | Unique identifier for a clinician. | Referenced across OMOP tables to link care to specific individuals. |
| `provider_name` | Clinician name | Optional name field (often redacted or omitted in de-identified data). | Can help with quality assurance or small internal datasets. |
| `npi` | National Provider Identifier | Standard U.S. NPI (if available). | Useful for linking to external registries or credentialing sources. |
| `dea` | DEA number | Drug Enforcement Administration number (if available). | Rarely used, but may be relevant in controlled substance research. |
| `specialty_concept_id` | Provider specialty | Standard concept (e.g., "Internal Medicine", "Medical Oncology"). | Enables provider-type stratification or filtering. |
| `care_site_id` | Affiliated facility or clinic | Foreign key to the `care_site` table. | Identifies where the provider primarily practices. |
| `year_of_birth` | Year of birth | Not commonly populated. May be used in workforce analytics. | Rare in EHR exports. |
| `gender_concept_id` | Provider gender | Gender identity (e.g., Male, Female). May be sourced from HR systems. | Sometimes used in workforce equity analyses. |
| `provider_source_value` | Local provider ID | The original ID from the source system (e.g., PROVIDER_ID in Clarity). | Essential for mapping and QA. |
| `specialty_source_value` | Raw specialty string | Source system’s specialty value (e.g., “Cardiology”). | Used to validate `specialty_concept_id`. |
| `specialty_source_concept_id` | Mapped concept from source | Concept ID derived from source specialty. | Supports consistency with OMOP vocabularies. |

### Common Pitfalls and What to Watch For

- **Not always linked across all events**: Many OMOP ETLs omit `provider_id` from certain events (e.g., condition, drug) if the source doesn't track it.
- **Provider names often redacted**: Especially in limited or de-identified datasets.
- **Specialty mapping may be shallow**: Specialty codes vary by EHR, and Emory's is unfortunately a bit limited.

### Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| Which providers see the highest number of patients with uncontrolled diabetes? | `measurement` + `condition_occurrence` + `provider_id` |
| What is the prescribing pattern for opioids by provider specialty? | `drug_exposure` + `provider.specialty_concept_id` |
| How many procedures were performed by general surgeons last year? | `procedure_occurrence` + `provider.specialty_concept_id` |
| Are there differences in documentation patterns between provider types? | `note` + `provider_id` + `note_type_concept_id` |
| Can we profile care delivery patterns at the provider vs care site level? | Any clinical event table + `provider_id` + `care_site_id` |