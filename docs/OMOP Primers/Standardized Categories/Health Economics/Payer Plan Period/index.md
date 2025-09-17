## 💳 Payer Plan Period

The `payer_plan_period` table captures **the span of time during which a patient was covered by a specific insurance plan or payer**. This is especially important in claims-based datasets but can also appear in EHRs with linked registration or benefits data.

In Epic or Cerner, this information comes from **registration/ADT systems** or **insurance eligibility feeds**. Each row represents a **continuous period of insurance enrollment or coverage** under a specific plan or payer.

This table is essential for identifying **study eligibility**, **payer-type stratification (e.g., Medicare vs commercial)**, and **longitudinal analyses of access to care**.

### Key OMOP Fields Mapped to Familiar EHR/Claims Concepts

| OMOP Field | EHR Analogy | Description | Clinical Relevance |
|------------|-------------|-------------|---------------------|
| `payer_plan_period_id` | Coverage segment ID | Unique ID for the coverage period. | Used internally for joining across time. |
| `person_id` | Patient ID | Foreign key to `person`. | Ties the coverage window to the individual. |
| `payer_plan_period_start_date` | Coverage start date | First day the insurance plan was active for the patient. | Used for defining eligibility windows. |
| `payer_plan_period_end_date` | Coverage end date | Last day of plan coverage. | Used for censoring or risk window definitions. |
| `payer_concept_id` | Payer organization | Standard concept for the payer (e.g., "Medicare", "Private payer"). | Enables payer-type stratification in studies. |
| `payer_source_value` | Raw payer name/code | Payer name or ID from the source system. | Useful for QA or site-specific filtering. |
| `payer_source_concept_id` | Mapped concept for payer | Concept derived from the source code. | Sometimes used for deeper categorization. |
| `plan_concept_id` | Plan type (e.g., HMO, PPO) | Standard concept representing the plan type or product. | Can be used to group plan characteristics across systems. |
| `plan_source_value` | Local plan name or code | Original plan name or ID (e.g., "BlueCross HMO Plan 2023"). | Helps with crosswalks and QA. |
| `plan_source_concept_id` | Mapped concept for plan | Concept ID for standardized plan type. | Useful when aggregating across product lines. |
| `sponsor_concept_id` | Employer or group sponsor | Standard concept for the sponsoring organization (if tracked). | Rarely populated, but may be useful for employer-sponsored plan analyses. |
| `sponsor_source_value` | Raw sponsor name/code | Local employer or group code. | Mostly relevant for commercial claims datasets. |
| `sponsor_source_concept_id` | Mapped concept for sponsor | Concept ID representing employer, union, or group. | Seldom populated unless deeply enriched. |
| `family_source_value` | Family group code | ID tying patient to family coverage (e.g., household policy). | May be useful in pediatric, dependent, or household analyses. |

### Common Pitfalls and What to Watch For

- **Often absent in pure EHR datasets**: This table is usually only populated when linked with claims or registration data. Emory is no exception. Please see Known Quality Issues for updates.
- **No claim detail here**: This table represents *coverage*, not actual service-level claims.
- **Start/end gaps can exist**: Validate continuity before assuming a patient is “continuously enrolled”.

### Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| How many patients had continuous Medicaid coverage during pregnancy? | `payer_plan_period.payer_concept_id` + coverage window + `condition_occurrence` |
| Are there differences in cancer screening rates by insurance type? | `payer_concept_id` + `procedure_occurrence` or `measurement` |
| What is the average duration of commercial plan enrollment? | `payer_plan_period_end_date` − `payer_plan_period_start_date` |
| Can we define eligibility criteria based on at least 12 months of continuous coverage? | `payer_plan_period` duration filtering |
| Are high-cost therapies being accessed differently by Medicare vs private payers? | `drug_exposure` + `payer_concept_id` stratification |
