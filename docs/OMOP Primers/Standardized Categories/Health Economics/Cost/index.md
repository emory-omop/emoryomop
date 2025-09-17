## 💰 Cost

The `cost` table stores **financial data associated with healthcare events**, such as billed charges, allowed amounts, patient out-of-pocket payments, and payments made by third-party payers. Each row ties a **specific cost to a single clinical event**, such as a procedure, drug exposure, device use, or visit.

This table is heavily used in **claims-derived OMOP CDMs**, but may also be partially populated in enriched EHRs with **revenue cycle data or financial extracts**.

In Epic/Cerner, this might map to **professional or facility billing data**, **itemized charges**, or **accounting system exports** tied to service lines or claim segments.

### Key OMOP Fields Mapped to Familiar EHR/Claims Concepts

| OMOP Field | EHR/Claims Analogy | Description | Clinical Relevance |
|------------|---------------------|-------------|---------------------|
| `cost_id` | Cost record ID | Unique identifier for this cost entry. | Used internally. |
| `cost_event_id` | Linked event ID | The ID of the event the cost is associated with (e.g., a procedure or drug). | Links the cost to a clinical action. |
| `cost_domain_id` | Event type | Domain of the event (e.g., "procedure", "drug"). | Required to identify the correct source table. |
| `cost_type_concept_id` | Cost type | Concept describing the nature of the cost (e.g., billed, paid, negotiated rate). | Key for economic modeling. |
| `currency_concept_id` | Currency type | Currency used (e.g., USD). | Useful for international or multi-site comparisons. |
| `total_charge` | Billed amount | Original amount charged by the provider. | Represents list price. |
| `total_cost` | Allowed amount | Negotiated rate or payer-approved amount. | Represents what was actually paid. |
| `total_paid` | Paid amount | What was paid by all sources. | Includes patient, payer, supplemental payers. |
| `paid_by_payer` | Payer contribution | Amount covered by insurer or plan. | Critical for insurance burden analysis. |
| `paid_by_patient` | Patient payment | Includes copay, deductible, or coinsurance. | Key for out-of-pocket cost studies. |
| `paid_patient_copay`, `paid_patient_coinsurance`, `paid_patient_deductible` | Detailed patient responsibility | Granular breakdown of patient payments. | Helpful for equity studies or financial toxicity research. |
| `paid_by_primary`, `paid_by_secondary` | Payment sources | Breakdown of payment by payer priority. | Useful for dual-eligible or coordination of benefit analysis. |
| `payer_plan_period_id` | Linked coverage period | Links cost to a specific insurance enrollment. | Helps contextualize costs within eligibility window. |
| `drg_concept_id` | Diagnosis Related Group | Used in inpatient billing classifications. | Enables grouping of similar hospitalizations for cost comparison. |

### Common Pitfalls and What to Watch For

- **Often missing in EHR-only OMOP**: Most EHR systems do not include detailed cost data unless merged with claims. Emory is no exception. See Known Quality Issues for updates.
- **Costs can vary widely**: `total_charge` and `total_cost` are often very different—understand which is appropriate for your analysis.
- **Multiple cost rows per event**: Some events (e.g., surgeries) may have multiple cost components (e.g., facility, professional, anesthesia).

### Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| What is the average out-of-pocket cost for an MRI across regions? | `procedure_occurrence` + `cost.total_paid` + `paid_by_patient` + `location.zip` |
| Are there differences in payment structures between commercial vs Medicaid patients? | `cost` + `payer_plan_period.payer_concept_id` |
| What is the total cost of hospitalization for COVID-19 patients? | `condition_occurrence` + `visit_occurrence` + `cost.total_cost` |
| How does the cost of insulin therapy vary by plan type? | `drug_exposure` + `cost.total_cost` + `plan_concept_id` |
| What are the average copays for colonoscopy screening in high-deductible plans? | `procedure_occurrence` + `cost.paid_patient_copay` + `plan_concept_id` filter |