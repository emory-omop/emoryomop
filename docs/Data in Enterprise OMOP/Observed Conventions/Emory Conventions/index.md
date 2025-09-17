# Emory's Adoption of the Conventions and Local Additions

In order to ensure Emory is adhering to the principles of the CDM as an easy starting point for federated research in network studies around the globe, conventions created for the primary CDM tables, fields, and vocabularies by the OHDSI community **will not be modified**. However, there may be specific **additions** made to improve the experience of the Emory user community (e.g., data provenance and in-depth PII data for re-identification across sources, as allowable via a person's IRB approvals). It is notable that **OHDSI tools ignore additional columns and tables**, so individuals are encouraged to **add** whatever they need to make the tables more useful for their site, but modifications substantially intefere with the purpose of standardized methods for working with data by OHDSI tooling. Hence, the reason modifications to OHDSI conventions are not allowed.

## OMOP-CDM v5.4 Data Model Conventions

**Origin**: [OHDSI](https://ohdsi.github.io/CommonDataModel/dataModelConventions.html)

OHDSI's General, Vocabulary, and Mapping conventions are all observed by Emory, as of site access date, April 23, 2025. The following listed conventions are specific to Emory's implementation of the CDM and are not part of the standard OMOP CDM conventions.

## Tables

Several tables have been added to the CDM to support the needs of Emory's research community. These tables are not part of the standard OMOP CDM but have been created to enhance data management and analysis capabilities. The following tables have been added

**CARE_SITE_MAPPING** | **CDW** and **Epic**

| CDM Field | User Guide | ETL Convention | Datatype | Required | Primary Key | Foreign Key | FK Table | FK Domain |
|---|---|---|---|---|---|---|---|---|
| care_site_id | The unique identifier for the care site. | The care site ID is generated in this table for the final care_site omop table based first upon CDW care sites, then by appending incremental ID's for Epic care sites. This ID is **not stable between loads**. | Integer | Yes | Yes ||||
| care_site_source_value | the source value for the care site. | The source value differs between CDW and Epic with regard to FK values | varchar | Yes | No | CDW: location_key; Epic: pos_id | CDW: lkp_location; Epic: clarity_pos ||
| care_site_source | The database source | Hardcoded for either Epic or CDW sources. | varchar | Yes | No ||||


**LOCATION_MAPPING** | **CDW** and **Epic**

| CDM Field | User Guide | ETL Convention | Datatype | Required | Primary Key | Foreign Key | FK Table | FK Domain |
|---|---|---|---|---|---|---|---|---|
| location_id | The unique identifier for the location. | The location ID is generated in this table for the final location omop table based first upon CDW mapping table, then by appending incremental ID's for Epic mapping table. This ID is **not stable between loads**. | Integer | Yes | Yes ||||
| location_source_value | the source value for the location. | The source value differs between CDW and Epic with regard to FK values. Epic's source values are concatenated when present, or filled with '' if null. To save space in the FK column their `table.column` concatenations are included here: 1) PATIENT.ADD_LINE_1, 2) PATIENT.ADD_LINE_2, 3) PATIENT.CITY, 4) ZC_STATE.ABBR, 5) PATIENT.ZIP, 6) ZC_COUNTY.COUNTY_C | varchar | Yes | No | CDW: facility_key; Epic: see prior column | CDW: lkp_facility; Epic: see prior column ||
| location_source | The database source | Hardcoded for either Epic or CDW sources. | varchar | Yes | No ||||

**PROVIDER_MAPPING** | **CDW** and **Epic**

| CDM Field | User Guide | ETL Convention | Datatype | Required | Primary Key | Foreign Key | FK Table | FK Domain |
|---|---|---|---|---|---|---|---|---|
| provider_id |The unique identifier for the provider. | The provider ID is generated in this table for the final provider omop table based first upon CDW mapping table, then by appending incremental ID's for Epic mapping table. This ID is **not stable between loads**. | Integer | Yes | Yes ||||
| provider_source_value | the source value for the provider. | The source value differs between CDW and Epic with regard to FK values. Care was taken to dedupe providers between CDW or Epic where possible, but only based upon NPI at present given sparce details elsewhere available for trusted matching for deduplication. This issue is noted in known quality issues for the version release | varchar | Yes | No | CDW: personnel_id; Epic: prov_id | CDW: lkp_personnel; Epic: clarity_ser ||
| provider_source | The database source | Hardcoded for either Epic or CDW sources. | varchar | Yes | No ||||

**VISIT_OCCURRENCE_MAPPING** | **CDW** and **Epic**

| CDM Field | User Guide | ETL Convention | Datatype | Required | Primary Key | Foreign Key | FK Table | FK Domain |
|---|---|---|---|---|---|---|---|---|
| visit_occurrence_id | The unique identifier for the visit occurrence. | The visit occurrence ID is generated in this table for the final visit_occurrence omop table based first upon CDW mapping table, then by appending incremental ID's for Epic mapping table. This ID is **not stable between loads**. | Integer | Yes | Yes ||||
| visit_occurrence_source_value | the source value for the encounter | The source value differs between CDW and Epic with regard to FK values. Care was taken to dedupe providers between CDW or Epic where possible, but only based upon NPI at present given sparce details elsewhere available for trusted matching for deduplication. This issue is noted in known quality issues for the version release | varchar | Yes | No | CDW: encounter_key; Epic: 1) PAT_ENC_CSN_ID or 2) PAT_ENC_CSN_ID | CDW: fact_encounter; Epic: 1) PAT_ENC_HSP or 2) PAT_ENC ||
| visit_occurrence_source | The database source | Hardcoded for either Epic or CDW sources. | varchar | Yes | No ||||

### Hidden Tables

The following tables are viewable by only certain members of a research team, given the sensitive nature of the data. They are protected from view so cannot be found in the OMOP schema. The `schema.table` convention is therefore used to identify the table in our production database.

**OMOP_PII.PATIENT** | **CDW** and **Epic**

| CDM Field | User Guide | ETL Convention | Datatype | Required | Primary Key | Foreign Key | FK Table | FK Domain |
|---|---|---|---|---|---|---|---|---|
| person_id | The unique identifier for the person in the CDM. | The person_id is generated elsewhere as part of the ETL process (see ETL documentation), but is an FK here. This value **is stable** over time and across loads | integer | Yes | Yes | CDW: person_id; Epic: person_id | CDW: lkp_master_patient; Epic: deident_driver ||
| epic_pat_id | The unique identifier for the person in the Epic system. || varchar | No | No | Epic: epic_pat_id | Epic: patient ||
| pat_mrn_id | The "Emory MRN" for the person in the Epic system. | Historically, this should match the EMPI number in the CDW to the extent possible, but upon cleaning, it was known there were some collisions and problems with CDW data. It is therefore the case that while pat_mrn_id often matches empi_nbr, this is not always the case | varchar | No | No | Epic: pat_mrn_id | Epic: patient ||
| empi_nbr | The "CDW EMPI" for the person in the Cerner system. || varchar | No | No | CDW: empi_nbr | CDW: lkp_patient ||
| epic_pat_last_nm | The last name of the person in Epic. || varchar | No | No | Epic: pat_last_name | Epic: patient ||
| cdw_pat_last_nm | The last name of the person from the CDW. || varchar | No | No | CDW: patient_last_nm | CDW: lkp_patient ||
| epic_pat_first_nm | The first name of the person in Epic. || varchar | No | No | Epic: pat_first_name | Epic: patient ||
| cdw_pat_first_nm | The first name of the person from the CDW. || varchar | No | No | CDW: patient_first_nm | CDW: lkp_patient ||
| epic_birth_date | The date of birth of the person in Epic. || date | No | No | Epic: birth_date | Epic: patient ||
| cdw_birth_date | The date of birth of the person from the CDW. || date | No | No | CDW: birth_day_key | CDW: lkp_patient ||
| epic_middle_name | The middle name of the person in Epic. || varchar | No | No | Epic: pat_middle_name | Epic: patient ||
| cdw_middle_name | The middle name of the person from the CDW. || varchar | No | No | CDW: patient_middle_nm | CDW: lkp_patient ||
| epic_maiden_name | The maiden name of the person in Epic. || varchar | No | No | Epic: maiden_name | Epic: patient_2 ||
| cdw_maiden_name | The maiden name of the person from the CDW. || varchar | No | No | CDW: patient_maiden_nm | CDW: lkp_patient ||
| epic_sex | The sex/gender of the person in Epic. || varchar | No | No | Epic: name | Epic: zc_sex ||
| cdw_sex | The sex/gender of the person from the CDW. || varchar | No | No | CDW: gender_desc | CDW: lkp_patient ||
| epic_ssn | The social security number of the person in Epic. || varchar | No | No | Epic: ssn | Epic: patient ||
| cdw_ssn | The social security number of the person from the CDW. || varchar | No | No | CDW: patient_ssn_nbr | CDW: lkp_patient ||
| epic_zip | The zip code of the person in Epic. || varchar | No | No | Epic: zip_code | Epic: patient ||
| cdw_zip | The zip code of the person from the CDW. || varchar | No | No | CDW: patient_address_zip_desc | CDW: lkp_patient ||
| epic_email_address | The email address of the person in Epic. || varchar | No | No | Epic: email_address | Epic: patient ||
| cdw_email_address | The email address of the person from the CDW. || varchar | No | No | CDW: patient_email_address_nm | CDW: lkp_patient ||
| epic_phone_cell | The cell phone number of the person in Epic. | presently hardcoded to null as we do not have a reliable way to obtain this information in Epic | varchar | No | No ||||
| cdw_phone_cell | The cell phone number of the person from the CDW. || varchar | No | No | CDW: patient_phone_cell_nbr | CDW: lkp_patient ||

## Fields

### General

| Notation | Description |
|---|---|
| _source_primary_key | The primary key used to obtain the most granular data available to achieve distinct rows in the final OMOP table housing this column (e.g., `drug_exposure._source_primary_key`; `measurement._source_primary_key`). Aggregation across the primary key in a particular table may sometimes be required, as there are a great many changes to data in an EHR system that are not relevant to how data is represented in the OMOP-CDM. For example, a patient's drug order may have been ordered by one clinician, modified by the pharmacist, cancelled by staff by mistake, and then re-added by the pharmacist as they go to fulfil the order in the EHR. For operational purposes, these types of data are critical to capture, but for research questions we'll be addressing with the OMOP CDM, we are only interested in the final state of the drug order. |
| _source_primary_key_source | The exect location where the _source_primary_key was obtained. This should be nearly machine readable, with the caveate being aggregations. For aggregations, such as taking a minimum table primary key where all other values are distinct for the row save the PK of the `_source_primary_key`, the function used will be appended to the `database(or schema).table.column` name following a carot `^`. For example, if the source primary key is a minimum value of the `_source_primary_key_soure` column in the table, it will be represented as `database.table.column^min()`. For our continued example of a drug administration for epic data, this might be `clarity_onprem_omop.order_med.order_med_id^MIN()`. |