# 🧠 Note NLP

The `note_nlp` table contains **structured outputs from natural language processing (NLP) models applied to clinical notes**. Each row represents a **single extracted entity, concept, assertion, or relationship** found in the free-text stored in the `note` table.

This is how OMOP captures **derived insights from unstructured data**—such as identifying a mention of “shortness of breath” in a radiology report, extracting medication mentions, or classifying sentiment or temporality (e.g., "history of", "ruled out", "currently present").

Think of this table as the **bridge between clinical narrative and structured analytics**.

## Key OMOP Fields Mapped to NLP Pipeline Outputs

| OMOP Field | NLP Analogy | Description | Clinical Relevance |
|------------|-------------|-------------|---------------------|
| `note_nlp_id` | NLP record ID | Unique ID for the NLP output row. | Used internally. |
| `note_id` | Source note | Foreign key to `note`. Ties NLP outputs back to the original document. | Allows traceability and context re-linking. |
| `section_concept_id` | Note section type | Standard concept representing note section (e.g., HPI, Assessment). | Important for contextualizing mentions. |
| `snippet` | Context window | Short window of text around the concept mention. | Useful for validation and training. |
| `offset` | Character position | Location of the mention within the note text. | Can help reconstruct full sentence or use in visualization. |
| `lexical_variant` | Text phrase | The actual words used in the note (e.g., “SOB” or “shortness of breath”). | Needed for concept disambiguation. |
| `note_nlp_concept_id` | Mapped concept | Standard OMOP concept ID inferred from the lexical variant (e.g., SNOMED for a symptom). | Use for standardized concept-level analysis. |
| `note_nlp_source_concept_id` | Source vocabulary ID | If source vocabularies like UMLS or MedLEE were used, maps to original concept. | Useful in audit trails or multi-vocab pipelines. |
| `nlp_system` | NLP engine | Name of the tool used (e.g., cTAKES, MedLEE, BERT). | Important for reproducibility and metadata. |
| `nlp_date`, `nlp_datetime` | NLP run time | Date/time the NLP pipeline was run. | Tracks versioning or recency of processing. |
| `term_exists` | Presence indicator | Whether the concept is affirmed (1), negated (0), or uncertain. | Central to understanding assertion status. |
| `term_temporal` | Temporality | Indicates timing (e.g., current, history of, planned). | Helps determine clinical relevance. |
| `term_modifiers` | Qualifiers | Captures additional properties or flags (e.g., severity). | Allows deeper clinical interpretation. |

## Common Pitfalls and What to Watch For

- **One concept per row**: Multiple entities in one sentence = multiple rows. Don't assume a 1:1 with notes.
- **Tool-dependent structure**: Different NLP pipelines populate different fields—know what your source supports.
- **Concept granularity varies**: Mapping quality depends on NLP tool and dictionary versions.
- **Limited population and highly project specific at present**: See Known Quality Issues for updates.

## Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| What is the prevalence of symptom mentions (e.g., cough, fever) in COVID-era ER notes? | `note_nlp.note_nlp_concept_id` + `note.note_type_concept_id` + date filters |
| Can we identify undocumented but present comorbidities in narrative text? | `note_nlp` + `term_exists` = 1 + cross-check with `condition_occurrence` |
| How often are clinicians noting “goals of care” in free-text notes? | `note_nlp.lexical_variant` or `note_nlp_concept_id` for palliative terms |
| Are NLP-extracted pain assessments consistent with structured pain scores? | `note_nlp` + `measurement` (pain scale) |
| Can NLP detect trends in functional decline across progress notes? | `note_nlp_concept_id` for functional status + `nlp_date` |
