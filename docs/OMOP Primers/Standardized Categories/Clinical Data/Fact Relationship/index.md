# 🔗 Fact Relationship

The `fact_relationship` table captures **relationships between individual records (“facts”) across different OMOP tables**. This allows the model to express complex, sometimes hierarchical connections between events—such as linking a measurement to a procedure, connecting two conditions, or defining temporal sequences.

Think of this as the **glue for non-obvious relationships**—like associating a biopsy procedure with a pathology finding, or linking a pregnancy diagnosis to a delivery event.

In EHR terms, there often isn’t a single field that explicitly defines these relationships. Instead, they are **implied through timing, proximity, or clinical documentation**. OMOP makes these linkages explicit through this table.

### Key OMOP Fields Mapped to Familiar EHR Concepts

| OMOP Field | EHR Analogy | Description | Clinical Relevance |
|------------|-------------|-------------|---------------------|
| `domain_concept_id_1` | Source domain 1 | Identifies the domain of the first record (e.g., condition, procedure, measurement). | Required to locate the fact in the appropriate table. |
| `fact_id_1` | Record ID 1 | The ID of the record in the first domain table. | Links to the specific event (e.g., condition_occurrence_id). |
| `domain_concept_id_2` | Source domain 2 | Domain of the second record. | Enables cross-domain links. |
| `fact_id_2` | Record ID 2 | ID of the second event. | Used to tie it back to its source table. |
| `relationship_concept_id` | Type of relationship | Standard concept describing the nature of the link (e.g., “Has associated procedure”, “Is temporally after”). | Defines clinical or logical connection between facts. |

### Common Pitfalls and What to Watch For

- **Rarely populated by default**: Most standard ETL pipelines do not fill this table unless explicitly extended. Emory is no exception as this (see Known Quality Issues to monitor for updates).
- **No foreign key enforcement**: This is a “soft” join across tables—requires validation and thoughtful curation.
- **Concept ID-driven logic**: Must understand the relationship concept vocabulary to interpret the connection.

### Common Relationship Concept Examples

| Concept | Meaning |
|--------|---------|
| `44818770` – "Has associated finding (SNOMED)" | Links a procedure to a diagnosis that justified it. |

### Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| How are pathology results linked to biopsy procedures? | `fact_relationship` between `measurement` and `procedure_occurrence` |
| Which diagnoses were directly associated with a given surgery? | `fact_relationship` linking `condition_occurrence` to `procedure_occurrence` |
| Can we identify composite blood pressure readings from systolic + diastolic values? | `measurement` + `fact_relationship` (e.g., “is component of”) |
| How can we define a pregnancy episode from related diagnoses and procedures? | `condition_occurrence`, `procedure_occurrence`, `fact_relationship` with temporal link concepts |
| What conditions are explicitly tied to a device insertion? | `fact_relationship` between `condition_occurrence` and `device_exposure` |