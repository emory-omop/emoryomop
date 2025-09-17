# Overview
The OMOP common data model (CDM) is a standardized data model used in the Observational Health Data Sciences and Informatics (OHDSI) community. It provides a common structure for storing and analyzing healthcare data from various sources, enabling researchers to conduct large-scale observational studies. The CDM is designed to facilitate the integration of data from different databases, allowing for the generation of real-world evidence and insights into healthcare practices and outcomes.

The OHDSI conventions are a set of guidelines and best practices for using the OMOP CDM. These conventions help ensure consistency and standardization in the way data is represented and analyzed within the CDM framework. They cover various aspects of data modeling, including data types, vocabulary usage, and data transformation processes.

You will find documentation regarding the conventions used in a wide variety of sources given the open source nature of the CDM conventions. However, the community is attempting to consolidate the information where possible. The following links are a good starting point for understanding the conventions:

- [OMOP CDM Documentation](https://ohdsi.github.io/CommonDataModel/)
  - Emory is presently using [version 5.4 of the CDM](https://ohdsi.github.io/CommonDataModel/cdm54.html)
- [General CDM conventions](https://ohdsi.github.io/CommonDataModel/dataModelConventions.html)
- [THEMIS Convention Library](https://ohdsi.github.io/Themis/)
- Dataset specific conventions
  - Conventions surrounding specific data types or patient groups may be associated with the efforts of a particular [workgroups](https://ohdsi.org/workgroups/). Some common examples include the Healthcare Systems Interest Group, responsible for developing conventions for the use of the CDM in healthcare systems, and the Oncology Workgroup, which focuses on conventions for patient oncology data. These workgroups often publish their conventions in the form of white papers or technical reports, which can be found on the OHDSI website or through academic publications.
- [OHDSI Forums](https://forums.ohdsi.org/)
  - The OHDSI forums are a valuable resource for discussions and questions related to CDM best practices by the community and outside of the workgroups, are likely to be the most common source for collaborative development of best practices still in development.

**NOTE**: Emory follows the primary stated conventions to the extent known for the General and THEMIS conventions (the stated best practices of the OHDSI community), but there are known convention adherence issues given workload of our team. See [Convention Adherence]().