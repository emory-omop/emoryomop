# Standardized Clinical Data

The purpose of this section is to help streamline understanding of the OMOP structure from the perspective of someone with experience in interacting with Cerner and Epic databases. The goal is to provide a high-level overview of the OMOP structure, including the tables and their relationships, as compared to the familiar frameworks of an EHR system.

The structure of the following tables is organized by OMOP table, and goes along with the CDM table flow (i.e., person then observation_period then visit_occurrence, etc). The tables themselves then describe the OMOP field, an EHR approximate analogue, a brief description, followed by clinical relevance. This primer supplements but does not replace the OMOP CDM documentation and OHDSI Conventions. Furthermore, some columns may be left out if they were not deemed particularly relevant for the purposes of the primer. Once you start coming up with your own research questions, it will be important that the official documentation is referenced.

The tables discussed in this document are included in the blue "Standardized Clinical Data", red "Standardized Health System", and some aspects of the purple "Standardized Derived Elements" sections of the OMOP v5.4 diagram below.

**NOTE**: In contrast to EHR systems which are typically internally organized around encounter events, the OMOP CDM is organized around **individual patients** via the `person` table centric structure.

![simple_erd](../../assets/images/cdm54.png)
