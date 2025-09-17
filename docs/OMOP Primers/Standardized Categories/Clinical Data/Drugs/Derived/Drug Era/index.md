# 💊:material-tape-measure:📆 Drug Era

The `drug_era` table represents **aggregated, inferred periods during which a patient was continuously exposed to a particular drug or drug class**. It consolidates multiple individual `drug_exposure` records into a **single era of use**, adjusting for gaps and overlaps based on rules defined in the ETL process.

This is similar to how clinicians think of **“being on a medication”**, even if refills or administrations were fragmented in the record.

The `drug_era` table is **not sourced directly from EHR or claims**, but is derived during ETL from the `drug_exposure` table using a **persistence window** (e.g., 30-day gap between fills before ending an era).

### Key OMOP Fields Mapped to Familiar Clinical Concepts

| OMOP Field | Clinical Analogy | Description | Clinical Relevance |
|------------|------------------|-------------|---------------------|
| `drug_era_id` | Medication episode ID | Unique identifier for the drug era. | Used internally for joins or summarization. |
| `person_id` | Patient ID | Foreign key to `person`. | Links the era to the patient. |
| `drug_concept_id` | RxNorm ingredient or class | Concept representing the drug used (e.g., "Metformin"). | Use for class-based analyses, rather than brand-specific events. |
| `drug_era_start_date` | Therapy start date | First day the patient is considered exposed. | Serves as anchor for treatment duration or time-at-risk logic. |
| `drug_era_end_date` | Therapy end date | Last date of exposure, allowing for inferred continuation. | Used to calculate duration of use. |
| `drug_exposure_count` | Number of records collapsed | Total number of raw exposures contributing to the era. | Helps distinguish long use from one-time events. |
| `gap_days` | Total days between exposures | Combined gap days tolerated during the era. | Indicates fragmentation or refill behavior. |

### Common Pitfalls and What to Watch For

- **Derived, not raw**: Drug eras are inferred and may not align exactly with prescription dates.
- **Gap logic is customizable**: Different ETLs use different thresholds (e.g., 30-day gap between fills); check your site’s settings.
- **Drug class mapping**: Uses ingredient-level concepts; not always at the NDC or brand name level.

### Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| How long are patients typically on statin therapy? | `drug_era` filtered for statin concepts + `drug_era_end_date` − `drug_era_start_date` |
| What proportion of patients discontinue metformin within the first year? | `drug_era` (metformin) + filtering by duration |
| Can we identify treatment-free intervals in oncology drug classes? | `drug_era` filtered for chemo agents + gaps between eras |
| How many diabetes patients had overlapping insulin and GLP-1 agonist use? | `drug_era` with class-level concepts + overlap logic |
| Are certain medications more likely to have fragmented adherence patterns? | `gap_days` + `drug_exposure_count` in `drug_era` |
