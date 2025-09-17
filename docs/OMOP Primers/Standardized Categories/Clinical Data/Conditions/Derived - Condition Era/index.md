# 📆 Condition Era Table

The `condition_era` table aggregates **periods of continuous presence or recurrence of a particular condition** for a patient. It rolls up multiple `condition_occurrence` events—using gap and overlap logic—into **a single episode (era) of disease**.

Think of it as representing the **overall span of time a patient is considered to have a condition**, smoothing out repeated diagnostic codes for chronic or recurrent diseases (e.g., diabetes, hypertension, depression).

`condition_era` is built during ETL from the `condition_occurrence` table using a **persistence window** (e.g., 30 days between codes before ending an era).

### Key OMOP Fields Mapped to Familiar Clinical Concepts

| OMOP Field | Clinical Analogy | Description | Clinical Relevance |
|------------|------------------|-------------|---------------------|
| `condition_era_id` | Disease episode ID | Unique identifier for the condition era. | Used internally for era-level joins. |
| `person_id` | Patient ID | Foreign key to `person`. | Links the era to the patient. |
| `condition_concept_id` | SNOMED condition | Standardized concept for the disease (e.g., "Hypertension"). | Enables disease-level analysis rather than visit-level coding. |
| `condition_era_start_date` | First indication date | First date patient is considered to have the condition (start of era). | Use for cohort entry or outcome windows. |
| `condition_era_end_date` | Last indication date | Last date patient is considered to have the condition (end of era). | Use for calculating duration or censoring. |
| `condition_occurrence_count` | Number of codes merged | Total number of `condition_occurrence` records collapsed into the era. | Distinguishes chronic from one-off/episodic disease. |

### Common Pitfalls and What to Watch For

- **Gap logic is configurable**: Default is often 30 days—check local ETL settings.
- **Chronic conditions may show long eras**: e.g., diabetes may have a single long era from diagnosis onward.
- **Era ≠ episode of care**: Represents period of disease presence, not necessarily active care.

### Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| What is the median duration of major depression in our cohort? | `condition_era` (major depression) + end_date − start_date |
| How many hypertensive patients had a gap in diagnosis >1 year? | `condition_era` (hypertension) + gap or count logic |
| Can we identify incident vs prevalent cases of asthma? | `condition_era` start_date relative to cohort entry date |
| Are certain conditions more likely to recur after a gap? | `condition_era` with multiple eras per person/concept |
| What is the comorbidity burden (count of concurrent condition eras) at index date? | `condition_era` overlapping on cohort start date |