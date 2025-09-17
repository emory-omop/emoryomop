# 🧷 Episode Event

The `episode_event` table links **individual clinical events**—like a diagnosis, procedure, drug, or measurement—to a broader **clinical episode** defined in the `episode` table. It allows you to **reconstruct the timeline and components of a high-level episode** by connecting granular facts back to their episode container.

This table is critical for **longitudinal studies**, **phenotyping algorithms**, and **multi-domain bundles** like prenatal care, chemotherapy regimens, or stroke episodes.

Each row represents a **link between a single clinical event and a specific episode**.

### Key OMOP Fields Mapped to Familiar Clinical Concepts

| OMOP Field | Clinical Analogy | Description | Clinical Relevance |
|------------|------------------|-------------|---------------------|
| `episode_event_id` | Link ID | Unique identifier for this episode-event link. | Used internally for joins. |
| `episode_id` | Parent episode | Foreign key to `episode.episode_id`. | Associates this event with a defined clinical episode. |
| `event_id` | Child event | The ID of the event being linked (e.g., procedure_id, condition_occurrence_id). | Points to the specific fact being grouped. |
| `event_field_concept_id` | Source domain + field | Standard concept describing which table and field the event ID comes from (e.g., “Procedure Occurrence ID”). | Necessary to locate and join the correct event record. |

### Supported `event_field_concept_id` Domains

These define where to look for the `event_id`. Common examples include:

| Concept | Maps to Table |
|---------|----------------|
| `1147126` – Device exposure ID | `procedure_occurrence` |
| `1147127` – Condition occurrence ID | `condition_occurrence` |
| `1147094` – Drug exposure ID | `drug_exposure` |

### Common Pitfalls and What to Watch For

- **Only useful if `episode` table is populated**: This table is essentially meaningless without episode context.
- **Must resolve `event_field_concept_id` to domain**: To query linked events, you need to know which domain to join.
- **Multiple events per episode**: Episodes can have many associated facts—this table enables full reconstruction.

### Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| What diagnoses and procedures occurred during pregnancy episodes? | `episode` (pregnancy) + `episode_event` + join to `condition_occurrence`, `procedure_occurrence` |
| Which lab results contributed to the staging of oncology episodes? | `episode_event` linking `measurement` records to `episode_concept_id` = cancer |
| How many days passed between the first and last drug in each chemotherapy cycle? | `episode_event` + `drug_exposure` filtered by episode ID and event dates |
| Can we quantify the burden of care during stroke episodes? | `episode_event` count of linked events + `episode` filtered for stroke |
| What is the distribution of visits associated with complex surgical episodes? | `episode_event` + `visit_occurrence` + filter by `episode_concept_id` |