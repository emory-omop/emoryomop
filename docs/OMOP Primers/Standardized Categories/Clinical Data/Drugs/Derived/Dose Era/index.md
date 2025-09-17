# 💊📆 Dose Era

The `dose_era` table represents **aggregated periods during which a patient was exposed to a consistent daily dose of a particular drug**. Like `drug_era`, it is derived from the `drug_exposure` table, but with an additional layer of **dose normalization and grouping**.

In clinical terms, this approximates periods when a patient was **on a stable medication regimen**, allowing researchers to compare exposure intensity across patients, stratify by dose level, and evaluate dose-dependent effects.

This table is especially useful in **pharmacoepidemiology**, **dose-response studies**, and **safety signal detection**.

### Key OMOP Fields Mapped to Familiar Clinical Concepts

| OMOP Field | Clinical Analogy | Description | Clinical Relevance |
|------------|------------------|-------------|---------------------|
| `dose_era_id` | Dose exposure ID | Unique identifier for the dose era. | Internal reference. |
| `person_id` | Patient ID | Foreign key to `person`. | Associates the dose period with the patient. |
| `drug_concept_id` | RxNorm ingredient | Standard concept for the drug (e.g., "Lisinopril"). | Enables ingredient-level grouping and dose comparisons. |
| `unit_concept_id` | Unit of dose | Concept for units like mg, mL, etc. | Essential for dose normalization. |
| `dose_value` | Daily dose | Numeric value representing the standardized daily dose during this era. | Allows for dose-response modeling. |
| `dose_era_start_date` | Dose period start | First date of stable dosing. | Used for longitudinal tracking and comparison. |
| `dose_era_end_date` | Dose period end | Last date of the dose era. | Helps calculate duration of exposure. |

### Common Pitfalls and What to Watch For

- **Only includes consistent dosing**: Gaps or changes in dose result in separate eras.
- **Less frequently populated**: Many OMOP ETLs omit this table unless explicitly configured.
- **Dependent on accurate dose capture**: Poor or missing dose data in `drug_exposure` will limit utility.

### Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| What is the average duration of high-dose corticosteroid use in IBD patients? | `condition_occurrence` (IBD) + `dose_era` filtered for prednisone and dose_value > threshold |
| Are there dose-dependent safety signals for statins and rhabdomyolysis? | `dose_era` (statins) + `condition_occurrence` (rhabdo) |
| How often do patients escalate vs de-escalate antidepressant doses? | `dose_era` (antidepressants) + sequence logic using start/end dates and dose_value |
| What proportion of patients maintain a stable insulin dose for ≥ 90 days? | `dose_era` (insulin) + filter by duration |
| Can we link dose changes to changes in blood pressure or lab values? | `dose_era` + `measurement` (BP or labs) before/after dose change |
