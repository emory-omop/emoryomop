# Overview
In this document you will find known issues organized by OMOP table at the time of release (see top of document). The issues are organized by table according the CDM architecture diagram.

![simple_erd](../../../assets/images/cdm54.png)

The status on these issues should be documented first in jira issues, and you can find best practices for interacting with Jira via the [Jira Release Notes](../../Releases/Jira%20Release%20Notes/index.md) document.

## Issues, Ramifications, and Best Practice Guidelines

We do our best to list known ramifications that our team considered, as well as our recommendations for best practices when using the impacted data. If you have additional suggestions it is strongly encouraged that you contact us for further discussion and additions to the following documentation. There are times when this structure isn't followed, as documentation of these issues is prioritized over visual design elements and consistency.

### General
These issues are cross cutting across more than one table, and are known issues with the data at Emory

- Sex vs. Gender
    - **Issue**: It has been observed that patients that could possibly have a demarcation between sex and gender (e.g., undergone reassignment) have been listed in Emory EHR data (both CDW and Epic) as either their present gender identity (via transition or otherwise) or their sex assigned at birth.
    - **Ramifications**: This could lead to misinterpretation of the data, especially in the context of projects that are carefully considering gender identity. Sex assigned at birth is not guaranteed, but is often used for many Emory research projects outside of OMOP without further assessment of the sex vs. gender coding confirmation.
    - **Best Practice Guidelines**: 
        - Use the `gender_concept_id` field in the `person` table with this known limitation in mind that it could represent either sex assigned at birth or gender identity, or transitioned gender. Many researchers are aware of this and for most projects, find this acceptable for basic demographic breakdowns where sex differences are not the primary variables under study.

- _source_primary_key_source
    - **Issue**: at time of release (see top of document) the source_primary_key_source field was not consistent across all tables. This is a known issue and will be addressed in future releases.
    - **Ramifications**: This could lead to confusion when trying to identify the source of a record, especially when joining tables or analyzing data.
    - **Best Practice Guidelines**: 
        - In some cases where there is not an `database.schema.table.column` (or with the additional `^MIN()` where applicable), users should go back to ETL documentation to be certain of the provenance of the data.

- For Epic, poor concept representation is standard some tables and columns (i.e., `{table}.{property}_concept_id`)
    - **Issue**: The concept_id's used in the `{table}.{property}_concept_id` fields are not always the best representation of the data as of the current release. Where the source data in Epic used common medical ontologies (e.g., ICD-10, CPT4) we were able to easily map the data to an OMOP standard vocabulary (e.g., ICD-10 to SNOMED; NDC to RxNorm). For custom ontologies unique to Emory and unique to Epic, we were only able to map a small fraction of the source data at time of release (see top of document).
    - **Ramifications**: Some data types will not be amenable for use in OHDSI tooling requiring standard ontologies, such as ATLAS or Cohort Builder.
    - **Best Practice Guidelines**: 
        - For common GUI tools (e.g., ATLAS), rely upon building cohorts with well mapped ontologies that have utilized medical ontologies in the CDW or Epic system.
        - For local ontologies without a medical ontology that is used in Epic, work with an analyst to directly query the OMOP database, and utilize the correlary `{table}.{property}_source_value` field to utilize the normal source data brought into OMOP directly from clarity.
        - Consider providing custom concept mapping for concepts critical to your research by utilizing the Custom Concept SOP. This will greatly expedite the process of getting custom concepts into the OMOP database, and will allow you to utilize the standard OMOP vocabulary for your research.
        - Consider reviewing the Current Concept Coverage document to ensure you are familiar with fields that remain poorly mapped, and decide when research will either need to target `{table}.{property}_source_value` or creating custom concepts.

### Person

- Basic demographics for person (gender, ethnicity, race) are not mapped due to lack of medical ontologies
    - this is a high priority for mapping and will be addressed in the next release. For now, source_value's can be utilized in the interim for Epic data.

### Observation Period

- The lack of data in some other tables (e.g., Epic vitals) at the time of current release will influence observation period if the measurment occurs at either the start or end of a patients history at Emory.

### Visit Occurrence

- place of service, discharged to, and admitted from concepts are not well mapped at present due to lack of standard medical ontologies. 
    - Algorithm to achieve improved granularity mapping is being worked on in future releases.

### Visit Detail

- Limited granularity at present. This may be a focus of future work. Continue to check our Releases for updates.

### Condition Occurrence

- No known issues at present. Epic's use of medical ontologies made mapping relatively easy for this table.

### Drug Exposure

- Units and routes are not well mapped at present due to lack of standard medical ontologies. 
- Ingredient is the most common level of granularity for standard concept mapping as these were the most consistent across drugs in Epic. 
    - Algorithm to achieve improved granularity mapping is being worked on in future releases.

### Procedure Occurrence

- No known issues at present. Epic's use of medical ontologies made mapping relatively easy for this table.

### Device Exposure

- Not mapped to a very large extent
    - **Issue**: The device exposure table is not mapped to a very large extent at time of release (see top of document), and this is particularly true for Epic as this table is currently missing from that side of the ETL (see ETL documentation).

### Measurement

- Not mapped to a very large extent for Epic at time of present release
    - **Issue**: The measurement table is not mapped to a very large extent at time of release (see top of document) for Epic as the source tables very rarely included medical ontologies easily mapped to standard concepts. Excepts include lab values (e.g., LOINC codes). See ETL documentation for greater details.
    - **Ramifications**: This could lead to missing data when analyzing measurements, especially in the context of projects that are carefully considering measurement data.
    - **Best Practice Guidelines**: 
        - Use the measurement table as a reference for data, but even source data due to ETL requirements, is missing whether or not concept mapped. For example, vitals (BP, Height, Weight, RR) were not included as they were not yet mapped to a standard concept.
            - This is high priority for our group to resolve these basic mappings
        - Consider using additional filters or deduplication methods when analyzing measurement data.
        - Recognize that research questions based on Epic measurement data may be limited outside of labs (or other discrete medical ontologies used in Epic) at the present time.
- Units are not well mapped at present due to lack of standard medical ontologies. Additionally, where they are available, they may differ between observations. This is a break from conventions (see Convention Adherence documentation for more)

### Observation

- Not concept mapped to a very large extent for Epic data as there were very few medical ontologies utilized in the source data for these tables. Notable exceptions include conditions and procedures with normalized ontologies (CPT4; ICD-10) that per the OHDSI community, is best mapped to the "Observation" domain.

### Death

- Cause of Death not presently mapped as this data is not readily available from current sources
- Death dates are not currently being udpated from the state registry for new Epic patients from 2022+ due to changes in how our contacts can provide the data to us. This is being investigated actively and may be addressed in an upcoming release.

### Note

- Not mapped
- High priority given medical information desired, and so will be a focus for us in the near future. 
    - Continue to monitor the Product Roadmap for updates.

### Note NLP

- Not mapped
- Unified enterprise wide NLP ingestion is a challenge, but is likely a worthwhile activity desired by researchers. Be on the look out for Product Roadmap updates.

### Specimen

- Not mapped to a very large extent for Enterprise OMOP
    - **Issue**: The specimen table is not mapped to a very large extent at time of release (see top of document), and this is particularly true for Epic as this table is currently missing from that side of the ETL (see ETL documentation).

### Fact Relationship

- Not utilized at time of release (see top of document)

### Location

- Latitude and longitude data via mechanisms such as GIS are not performed at present

### Care Site

- Granularity is not very deep

### Provider

- Duplications
    - **Issue**: One human may have multiple provider_id's and provider_source_value's. Dedupe between CDW and Epic was not completed for a majority of cases as required data to ensure a match, such as NPI, was not available in all cases.
    - **Ramifications**: provider counts will be inflated, and when data is joined to the provider table, previously distinct rows could change granularity due to the 1 provider to many ID's available between CDW and Epic merge.
    - **Best Practice Guidelines**: 
        - Use the provider table as a reference for provider IDs, but be cautious when joining to other tables or when analyzing the results
        - Consider using additional filters or deduplication methods when analyzing provider data.

### Payer Plan Period

- Not mapped

### Cost

- Not mapped

### Drug Era

- No known issues at present.

### Dose Era

- Dose units should be standardized to the same unit of measure (e.g., mg) for all of a particular drug exposure in order to trust the dose_era fully. Right now, we are breaking from conventions (see Convention Adherence documentation for more). This table should therefore be used with caution.

### Condition Era

- No known issues at present.

### Episode

- Not mapped currently. Winship is likely to have a use case for chemotherapy regimens and is actively participating in a network study that utilizes Episodes that were constructed during study package implementation. Please reach out to Daniel (daniel.g.smith@emory.edu) for details.

### Episode Event

- Not mapped.

### Metadata

- This will be a critical table long-term between loads, and will provide important information for downstream stakeholders. A feature request to populate this table currently exists and is being considered during release planning (see Jira Release Notes and Product Roadmap for updates).

### CDM Source

- No known issues at present.