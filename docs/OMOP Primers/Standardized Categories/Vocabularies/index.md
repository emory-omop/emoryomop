# OMOP Primer for Vocabularies

The purpose of this document is to help streamline understanding of the OMOP structure from the perspective of someone with experience in interacting with Cerner and Epic databases. The goal is to provide a high-level overview of the OMOP vocabularies, including the tables and their relationships.

The structure of the following document is organized by OMOP table, and goes along with the CDM diagram flow (i.e., concept then vocabulary then domain, etc.). Each table's section describes the purpose of the table, and notes on usage at a very high level. This primer supplements but does not replace the OMOP CDM documentation and OHDSI Conventions. Furthermore, **if you don't intend to undergo training for anything else, *DO* complete training on the vocabularies if you intend to work with OMOP data**. See Education and Training for details on where to go for training.

The tables discussed in this document are included in the orange "Standardized Vocabularies" of the OMOP v5.4 diagram below:

![simple_erd](../../../assets/images/cdm54.png)

# 📖 Standardized Vocabulary Overview

The **OMOP Standardized Vocabularies** are the foundation that make it possible to do **apples-to-apples comparisons across different data sources**, even when the original data use different coding systems (like ICD, SNOMED, CPT, RxNorm, or NDC).

OMOP maps these codes to a **shared vocabulary** so researchers can define cohorts and concepts in a **consistent and reproducible way**, regardless of whether the data came from Epic, Cerner, Medicare claims, or anywhere else.

The vocabulary tables aren’t where you usually look for clinical data—but they are **what make OMOP work under the hood**. Even if you are not working on an OMOP project, you may even wish to use the mappings available in the OMOP vocabularies between medical ontologies (e.g., ICD-9 to SNOMED), or review the hierarchies so that you can easily select all descendant concepts off of a parent concept represented in both a standard and non-standard format (e.g., "give me all the )

**Special note on the word "standard"**: In the context of OMOP, "standard" refers to the codes that are chosen by the community by way of the CDM Workgroup -> vocabulary subgroup. The community members creating these mappings are often using ontologist's ready made tools to support their work (such as ICD to SNOMED mappings outside of OHDSI). Alternatively, they utilize expert consensus in the particular fields of medicine to create the mappings and identify the vocabularies that are the coordinated "standard" vocabulary. If issues with a mapping are identified via pressure testing by the global community of OMOP implementers, these may lead to a community remapping effort (typically discussed as logged github issues, discussion in the forums, or efforts of a particular workgroup to improve the ontology of interest to their work). For more details, please see OHDSI vocabulary working group's documentation and working repositories ([Vocabulary Github wiki](https://github.com/OHDSI/Vocabulary-v5.0/wiki)).

## 🧠 Concept

**Purpose**: Every concept (diagnosis, drug, lab, procedure, etc.) has a row in this table.
**Notes on use**: Think of it as a **dictionary of all the medical terms** OMOP understands. This will be one of the primary tables you use to find the right codes when building a study definition (e.g., all codes for “heart failure”).

## 🔗 Concept Relationship

**Purpose**: Defines relationships between concepts.
**Notes on use**: This table helps in understanding how different concepts are related to each other. For example, you'd use this table and the concept table to understand which codes are nested underneath larger concepts for a medical concept, such as "primary malignant neoplasm of the left upper lobe of lung" being associated with an anatomic site of left upper lobe of lung, which itself is related to lung. Also used to **map from source codes (ICD, CPT, etc.) to standard OMOP codes** by using the `relationship_id`'s between concepts, such as "is a" or "maps to". 

## 🌳 Concept Ancestor

**Purpose**: Helps find all the specific codes under a broader category that would otherwise be rather difficult via the use of concept relationship.
**Notes on use**: You can use this table to easily find all the descendant concepts of a given concept. For example, if you want to find all the codes related to "diabetes", you can use this table to get all the specific subtypes (like "type 1 diabetes", "type 2 diabetes", etc.) under that category without having to specify those children (like “type 2 diabetes with nephropathy”). This table is essential when creating cohort definitions using broad clinical categories.

## 🗺️ Source to Concept Map

**Purpose**: Maps local codes to standard OMOP concepts.
**Notes on use**: This table is used to map local codes without a common medical ontology to standard OMOP concepts. For example, internal Epic codes for "Female" do not use a common medical ontology, nor does "height" measurements. This is critical for Emory, and is intended to improve drastically over time.
**Special note**: We highly encourage collaboration on getting better and better mappings that are important to our users. Please see the Custom Concept SOP for details.

## Clinical Use Case Examples

| Question | Vocabulary Role |
|----------|------------------|
| I want to identify all patients with “heart failure” | Use `concept_ancestor` + `concept` to find the right standard codes |
| I need to include both ICD-9 and ICD-10 codes in my phenotype | Use `concept_relationship` to map source codes to standard ones |
| I’m building a cohort of patients on ACE inhibitors | Use `concept` (for RxNorm ingredients) + `concept_ancestor` (for all ACEs) |
| I want to understand if a procedure is surgical or diagnostic | Use `concept_relationship` or check `concept_class_id` in `concept` |
| I have a local lab code from our hospital—how can I use it in OMOP? | Check `source_to_concept_map` if it was mapped, or create a new mapping |