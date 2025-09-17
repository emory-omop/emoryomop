# 🧫 Specimen

The `specimen` table captures **information about biological samples collected from a patient**—such as blood, tissue, urine, swabs, or other materials sent for laboratory analysis, pathology review, or biobanking.

In Epic or Cerner, this aligns with **specimen collection records** tied to lab orders, surgical pathology, or research sample tracking systems. It may also be populated via **LIMS (Laboratory Information Management Systems)**, especially in precision medicine or oncology programs. At Emory, some partners, such as Winship, populate this table with cancer biospecimen data as part of their ETL pipeline via OpenSpecimen integration.

Each row represents **a single specimen** collected for diagnostic, therapeutic, or research purposes.

### Key OMOP Fields Mapped to Familiar EHR Concepts

| OMOP Field | EHR Analogy | Description | Clinical Relevance |
|------------|-------------|-------------|---------------------|
| `specimen_id` | Specimen accession ID | Unique identifier for the specimen. | Used for linking to tests, diagnoses, or omics data. |
| `person_id` | Patient ID | Foreign key to `person`. | Ties the sample to the patient. |
| `specimen_concept_id` | Sample type (e.g., blood, urine) | Standard concept for the specimen collected. | Important for identifying sample source. |
| `specimen_type_concept_id` | Source of data | Indicates how the specimen was captured (e.g., EHR, biobank). | Helps contextualize data lineage. |
| `specimen_date`, `specimen_datetime` | Collection date/time | When the sample was collected. | Anchors tests, diagnoses, or sequencing events. |
| `quantity` | Amount collected | Volume or size of sample (if available). | May be relevant for certain analytic workflows. |
| `unit_concept_id` | Unit of measure | Standard unit (e.g., mL, grams). | Important for quantitative analyses. |
| `anatomic_site_concept_id` | Collection site | Concept for body part or source site (e.g., "left breast", "bone marrow"). | Critical in pathology, cancer, or biopsy datasets. |
| `disease_status_concept_id` | Disease context | Indicates whether specimen was collected in disease state (e.g., tumor tissue). | Often used in case/control or molecular research. |
| `specimen_source_id` | Local specimen ID | Original ID from the lab system. | Useful for LIMS or crosswalk mapping. |
| `specimen_source_value` | Local name/code | Original specimen description from EHR/LIMS. | Helpful for QA, audits, or secondary mappings. |
| `unit_source_value` | Original unit | Local unit string, if not mapped to concept. | Useful for QA of `unit_concept_id`. |

### Common Pitfalls and What to Watch For

- **Often underused**: Many OMOP implementations don’t fully populate this table unless tied to biospecimen workflows.
- **Specimens ≠ measurements**: The actual lab values go into `measurement`—this table captures the sample metadata.
- **Omics-ready**: This table becomes critical when linking to sequencing or proteomics.

### Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| How many tumor tissue samples were collected from breast cancer patients? | `condition_occurrence` + `specimen.anatomic_site_concept_id` + `disease_status_concept_id` |
| Can we track whether blood was collected on the same day as a medication was started? | `specimen.specimen_date` + `drug_exposure.drug_exposure_start_date` |
| Which patients had CSF samples collected for diagnostic testing? | `specimen_concept_id` = CSF |
| How many archived FFPE samples exist for lung cancer cases? | `specimen_type_concept_id` = archived + `anatomic_site_concept_id` = lung + `condition_occurrence` |
| What is the average time from biopsy to pathology report documentation? | `specimen.specimen_date` + `note.note_date` (filtered for pathology notes) |