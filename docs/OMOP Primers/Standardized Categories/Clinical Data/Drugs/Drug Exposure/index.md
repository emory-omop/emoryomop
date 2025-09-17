# 💊 Drug Exposure

The `drug_exposure` table captures **medications a patient is recorded as having received or been prescribed**. This includes inpatient administered drugs, outpatient prescriptions, pharmacy claims, and even inferred exposures from e-prescribing systems.

In Epic/Cerner terms, this table draws from sources like **medication orders**, **MAR (med administration records)**, **prescription history**, and **outpatient pharmacy data**. It’s used for **medication usage analysis**, **adherence tracking**, **treatment pattern exploration**, and **cohort eligibility logic**.

Each row represents **a single medication exposure**, with as much granularity as available from the source system.

## Key OMOP Fields Mapped to Familiar EHR Concepts

| OMOP Field | EHR Analogy | Description | Clinical Relevance |
|------------|-------------|-------------|---------------------|
| `drug_exposure_id` | Medication record ID | Unique identifier for this drug exposure event. | Used internally for joins and grouping. |
| `person_id` | Patient ID | Foreign key to `person`. | Links drug use to the patient. |
| `drug_concept_id` | Standard RxNorm code | Standard concept ID for the medication. Enables cross-site and longitudinal analysis. | Always use for cohort definitions and standardized queries. |
| `drug_exposure_start_date`, `drug_exposure_end_date` | Start/end of medication | Dates when the patient was exposed to the drug. End may be null or inferred. | Critical for assessing exposure duration and treatment windows. |
| `drug_type_concept_id` | Route of entry | Identifies source/type of the data (e.g., EHR prescription, pharmacy dispense, claims fill). | Important for understanding reliability and context of data. |
| `stop_reason` | Cancel reason / Discontinue reason | Why the drug was stopped, if documented. Often not populated. | Useful in medication reconciliation or adherence studies. |
| `refills` | Refills authorized | Number of times the prescription could be refilled. | Can be used to estimate medication coverage or gaps. |
| `quantity` | Total amount | Total quantity dispensed or administered. | Helpful in dose standardization or exposure modeling. |
| `days_supply` | Days intended to last | Duration drug is intended to cover. Often populated in claims. | Used in adherence metrics (e.g., MPR, PDC). |
| `route_concept_id` | Oral / IV / Topical, etc. | Standard concept for route of administration. | Useful for differentiating formulations or clinical setting (e.g., IV chemo vs oral). |
| `sig` | Free-text prescription directions | Original instructions (e.g., "1 tab PO BID x 10d"). | Rarely analyzed directly, but helpful in context. |
| `visit_occurrence_id` | Associated visit | Encounter during which the medication was prescribed/administered. | Important for understanding setting of initiation. |
| `drug_source_value` | NDC or local drug code | The original code from the EHR system. | Useful for mapping QA or investigating local formulary nuances. |
| `drug_source_concept_id` | Source code concept | Maps the raw source code to a standard concept. | Secondary to `drug_concept_id` in standardized analysis. |

## Common Pitfalls and What to Watch For

- **Multiple rows for refills or administrations**: Some systems split each fill/admin into separate entries. These are aggregated to the extent captured by the enterprise OMOP team, but it is possible some slipped through. None are presently known however.
- **Missing route or days supply**: Not all EHRs populate these fields—especially for inpatient meds.
- **Null `drug_exposure_end_date`**: Often inferred by `days_supply`; may not reflect true treatment stop.

## Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| What proportion of hypertensive patients are taking ACE inhibitors? | `condition_occurrence` + `drug_exposure.drug_concept_id` filtered for ACE inhibitors |
| How long do breast cancer patients stay on endocrine therapy? | `condition_occurrence` + `drug_exposure` filtered for tamoxifen/aromatase inhibitors |
| Are statins being prescribed equitably across racial groups? | `person.race_concept_id` + `drug_exposure` filtered for statins |
| How many patients were given antibiotics during an ER visit? | `drug_exposure` + `visit_occurrence.visit_concept_id` = ER |
| Can we detect chemo cycles based on administration intervals? | `drug_exposure` filtered for antineoplastics, analyze timing via `drug_exposure_start_date` |