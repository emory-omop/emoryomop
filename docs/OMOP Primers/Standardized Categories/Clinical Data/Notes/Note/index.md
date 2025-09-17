# 📝 Note

The `note` table captures **unstructured clinical text**—such as progress notes, discharge summaries, radiology reports, pathology interpretations, and other narrative documentation written by clinicians.

In Epic or Cerner, this aligns with **clinical documentation**, **radiology reports**, or the **history and physical**. The contents are typically stored in **free text**, though downstream NLP pipelines can extract structured elements into OMOP's `note_nlp` table.

Each row represents **a single clinical note or report**, with metadata that includes the authoring provider, note type, date, and associated visit.

This table is critical for supporting **natural language processing (NLP)** efforts, **phenotyping**, and identifying documentation patterns not captured in structured fields.

## Key OMOP Fields Mapped to Familiar EHR Concepts

| OMOP Field | EHR Analogy | Description | Clinical Relevance |
|------------|-------------|-------------|---------------------|
| `note_id` | Note record ID | Unique identifier for the note. | Used internally and in `note_nlp` joins. |
| `person_id` | Patient ID | Foreign key to `person`. | Ties the note to the patient. |
| `note_date` | Note entry date | Date the note was authored. Time component in `note_datetime`. | Used for anchoring in timelines or pre/post analysis. |
| `note_type_concept_id` | Note category | Type of note (e.g., discharge summary, pathology report). Standardized using OMOP concepts. | Helps categorize free-text by context. |
| `note_class_concept_id` | Note class | Classifies the format or channel (e.g., dictation, transcription, structured note). | Useful for analyzing documentation workflows. |
| `note_title` | Note header or title | Original title as written in the EHR (e.g., "Follow-Up", "Operative Report"). | May be helpful in NLP preprocessing. |
| `note_text` | Full note body | The unstructured text of the note. | Used for NLP and qualitative review. |
| `encoding_concept_id` | Character encoding | Usually UTF-8. Not commonly queried. | Mostly metadata. |
| `language_concept_id` | Language of note | Typically English. Can be relevant for multi-lingual systems. | Supports localization and language-based NLP strategies. |
| `provider_id` | Author | Clinician who wrote or dictated the note. | Useful for attribution or signature audits. |
| `visit_occurrence_id` | Related visit | Encounter during which the note was created. | Helps place the note in clinical context. |
| `note_source_value` | Local note type or label | Original source value (e.g., "IM Progress Note"). | Useful for mapping QA and interpretability. |

## Common Pitfalls and What to Watch For

- **Large volume**: This table can be massive in EHR-derived datasets—use filters by date, type, or visit. It's best to avoid full table scans, and to get the presence/absence of a specific note type for a patient sample prior to later running queries to extract additional note information.
- **Unstructured content**: Hard to analyze without NLP; requires `note_nlp` or external tools to parse.
- **Note titles can vary by site/provider**: Standardizing requires mapping logic for `note_type_concept_id`.

## Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| How often do clinicians document advance directives in the free-text notes? | `note_text` with keyword/NLP search + `note_type_concept_id` |
| What is the sentiment of discharge summaries for palliative care patients? | `note_text` + `condition_occurrence` (palliative care) + NLP |
| Can we extract ECOG performance status from oncology notes? | `note_text` + NLP model + `condition_occurrence` (cancer) |
| How many patients have mentions of suicidal ideation in notes but not coded diagnoses? | `note_text` keyword search vs `condition_occurrence` |
| What are the most common note types authored in the ER setting? | `note_type_concept_id` + `visit_occurrence.visit_concept_id` = ER |