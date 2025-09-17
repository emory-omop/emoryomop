# Release notes - Enterprise OMOP Release Planning - 0.2.0 \(Enterprise OMOP Release\)

### Bug

[OMOPRP-9](https://emoryrhsit.atlassian.net/browse/OMOPRP-9) One Florida LOINC Code Errors

[OMOPRP-22](https://emoryrhsit.atlassian.net/browse/OMOPRP-22) Epic care\_site is empty

[OMOPRP-30](https://emoryrhsit.atlassian.net/browse/OMOPRP-30) condition status is mapped to type concept instead of status concept

[OMOPRP-33](https://emoryrhsit.atlassian.net/browse/OMOPRP-33) source\_to\_concept\_map incorrectly routed to omop\_etl\_epic

[OMOPRP-48](https://emoryrhsit.atlassian.net/browse/OMOPRP-48) DBT Test failures: condition\_occurrence

[OMOPRP-51](https://emoryrhsit.atlassian.net/browse/OMOPRP-51) Person Dupes associated with Location

[OMOPRP-52](https://emoryrhsit.atlassian.net/browse/OMOPRP-52) omop 5.3.1 -> 5.4 changes not fully implemented \(pt 1\)

[OMOPRP-69](https://emoryrhsit.atlassian.net/browse/OMOPRP-69) changes to CDW code needed for dedupe

[OMOPRP-73](https://emoryrhsit.atlassian.net/browse/OMOPRP-73) Changes to Epic code needed for dedupe

[OMOPRP-108](https://emoryrhsit.atlassian.net/browse/OMOPRP-108) DBT tests: Provider

[OMOPRP-109](https://emoryrhsit.atlassian.net/browse/OMOPRP-109) DBT tests: Drug

[OMOPRP-110](https://emoryrhsit.atlassian.net/browse/OMOPRP-110) DBT tests: Measurement

[OMOPRP-111](https://emoryrhsit.atlassian.net/browse/OMOPRP-111) DBT tests: Condition

[OMOPRP-112](https://emoryrhsit.atlassian.net/browse/OMOPRP-112) DBT tests: Death

[OMOPRP-113](https://emoryrhsit.atlassian.net/browse/OMOPRP-113) DBT tests: Person

[OMOPRP-114](https://emoryrhsit.atlassian.net/browse/OMOPRP-114) DBT tests: visit \(occurrence and detail\)

### Epic

[OMOPRP-2](https://emoryrhsit.atlassian.net/browse/OMOPRP-2) Quality Assurance \(improve data delivery\) \(Archive\)

[OMOPRP-3](https://emoryrhsit.atlassian.net/browse/OMOPRP-3) Quality Control \(manage product defects\) \(Archive\)

### Task

[OMOPRP-16](https://emoryrhsit.atlassian.net/browse/OMOPRP-16) Duplicates found in omop\_epic person, measurement, care\_site, and visit\_occurrence

[OMOPRP-17](https://emoryrhsit.atlassian.net/browse/OMOPRP-17) change person\_source\_value for epic to be pat\_mrn\_id \(unit test\)

[OMOPRP-27](https://emoryrhsit.atlassian.net/browse/OMOPRP-27) Patient data is strangely represented across tables

[OMOPRP-28](https://emoryrhsit.atlassian.net/browse/OMOPRP-28) Measurement Source Unit Truncation? Explore

[OMOPRP-29](https://emoryrhsit.atlassian.net/browse/OMOPRP-29) create small well represented sample of patients

[OMOPRP-31](https://emoryrhsit.atlassian.net/browse/OMOPRP-31) conditions exist without visits. Okay?

[OMOPRP-34](https://emoryrhsit.atlassian.net/browse/OMOPRP-34) ensure dbt docs have been pushed for each project

[OMOPRP-35](https://emoryrhsit.atlassian.net/browse/OMOPRP-35) observations exist without visits. Okay?

[OMOPRP-36](https://emoryrhsit.atlassian.net/browse/OMOPRP-36) measurement exists without visits. Okay?

[OMOPRP-37](https://emoryrhsit.atlassian.net/browse/OMOPRP-37) drug\_exposure: exists without visits?

[OMOPRP-38](https://emoryrhsit.atlassian.net/browse/OMOPRP-38) procedure\_occurrence exists without visits. Okay?

[OMOPRP-39](https://emoryrhsit.atlassian.net/browse/OMOPRP-39) create epic/cdw clinical data presence columns for all patients

[OMOPRP-40](https://emoryrhsit.atlassian.net/browse/OMOPRP-40) condition\_occurrence EDA

[OMOPRP-41](https://emoryrhsit.atlassian.net/browse/OMOPRP-41) Person EDA

[OMOPRP-42](https://emoryrhsit.atlassian.net/browse/OMOPRP-42) procedure\_occurrence EDA

[OMOPRP-44](https://emoryrhsit.atlassian.net/browse/OMOPRP-44) care\_site EDA

[OMOPRP-47](https://emoryrhsit.atlassian.net/browse/OMOPRP-47) drug\_exposure: ambulatory EDA

[OMOPRP-49](https://emoryrhsit.atlassian.net/browse/OMOPRP-49) drug\_exposure: anes EDA

[OMOPRP-50](https://emoryrhsit.atlassian.net/browse/OMOPRP-50) person automated QA: Human review

[OMOPRP-54](https://emoryrhsit.atlassian.net/browse/OMOPRP-54) visit\_occurrence automated QA: Human review

[OMOPRP-55](https://emoryrhsit.atlassian.net/browse/OMOPRP-55) drug\_exposure: hospital EDA

[OMOPRP-56](https://emoryrhsit.atlassian.net/browse/OMOPRP-56) Automation for Dbt\_results

[OMOPRP-57](https://emoryrhsit.atlassian.net/browse/OMOPRP-57) review dbt\_results - CDW

[OMOPRP-58](https://emoryrhsit.atlassian.net/browse/OMOPRP-58) verify omop load recency

[OMOPRP-59](https://emoryrhsit.atlassian.net/browse/OMOPRP-59) Figure out when to do the reload for issues team has found.

[OMOPRP-60](https://emoryrhsit.atlassian.net/browse/OMOPRP-60) Gain access to PHI

[OMOPRP-63](https://emoryrhsit.atlassian.net/browse/OMOPRP-63) Oncology readiness patch implementation

[OMOPRP-68](https://emoryrhsit.atlassian.net/browse/OMOPRP-68) Decision point: Is it okay to sunset split \(epic/cdw\) redshift instances?

[OMOPRP-71](https://emoryrhsit.atlassian.net/browse/OMOPRP-71) Review the changes to Redshift with downstream stakeholder

[OMOPRP-75](https://emoryrhsit.atlassian.net/browse/OMOPRP-75) project roadmap: 1st draft

[OMOPRP-76](https://emoryrhsit.atlassian.net/browse/OMOPRP-76) research data selection choice algorithm

[OMOPRP-88](https://emoryrhsit.atlassian.net/browse/OMOPRP-88) drug\_exposure: final table EDA

[OMOPRP-93](https://emoryrhsit.atlassian.net/browse/OMOPRP-93) Deploy Epic OMOP in Athena that is free from errors

[OMOPRP-94](https://emoryrhsit.atlassian.net/browse/OMOPRP-94) Deploy CDW OMOP in Athena that is free from errors

[OMOPRP-95](https://emoryrhsit.atlassian.net/browse/OMOPRP-95) Vocabulary reload

[OMOPRP-96](https://emoryrhsit.atlassian.net/browse/OMOPRP-96) Vocabulary CPT JAR run

[OMOPRP-99](https://emoryrhsit.atlassian.net/browse/OMOPRP-99) Deident\_driver update with deduped patients

[OMOPRP-103](https://emoryrhsit.atlassian.net/browse/OMOPRP-103) CDW rerun with new vocabularies for delta vocab check

[OMOPRP-119](https://emoryrhsit.atlassian.net/browse/OMOPRP-119) clear QA documentation table, like software comparison

[OMOPRP-120](https://emoryrhsit.atlassian.net/browse/OMOPRP-120) work on press releases with local community

[OMOPRP-124](https://emoryrhsit.atlassian.net/browse/OMOPRP-124) Update subsample

[OMOPRP-125](https://emoryrhsit.atlassian.net/browse/OMOPRP-125) procedure\_occurrence EDA rerun

[OMOPRP-126](https://emoryrhsit.atlassian.net/browse/OMOPRP-126) investigate measurement and other table diffs between loads in subsample

[OMOPRP-127](https://emoryrhsit.atlassian.net/browse/OMOPRP-127) Fix DBT according to Joan/Jessie/Jeselyn comments in fix excel file

[OMOPRP-130](https://emoryrhsit.atlassian.net/browse/OMOPRP-130) reload omop\_etl\_epic

[OMOPRP-131](https://emoryrhsit.atlassian.net/browse/OMOPRP-131) reload omop\_etl\_ent

[OMOPRP-132](https://emoryrhsit.atlassian.net/browse/OMOPRP-132) quick review of breaking dbt-tests for new epic load

[OMOPRP-133](https://emoryrhsit.atlassian.net/browse/OMOPRP-133) quick review of breaking dbt-tests for new enterprise load

[OMOPRP-134](https://emoryrhsit.atlassian.net/browse/OMOPRP-134) reload enterprise redshift

[OMOPRP-137](https://emoryrhsit.atlassian.net/browse/OMOPRP-137) Design general QA pipe with Subsample: Epic

[OMOPRP-138](https://emoryrhsit.atlassian.net/browse/OMOPRP-138) Create Subsample from Epic commit 2025-04-17

[OMOPRP-139](https://emoryrhsit.atlassian.net/browse/OMOPRP-139) Design general QA pipe with Subsample: CDW

[OMOPRP-140](https://emoryrhsit.atlassian.net/browse/OMOPRP-140) Create Subsample from CDW commit 2025-04-18

[OMOPRP-141](https://emoryrhsit.atlassian.net/browse/OMOPRP-141) Design general QA pipe with Subsample: Enterprise

[OMOPRP-143](https://emoryrhsit.atlassian.net/browse/OMOPRP-143) Create Subsample from ENT commit 2025-04-18

[OMOPRP-152](https://emoryrhsit.atlassian.net/browse/OMOPRP-152) Concept Mapping Assessment \(>0\)

[OMOPRP-153](https://emoryrhsit.atlassian.net/browse/OMOPRP-153) Concept Mapping Assessment \(top 10 concepts\)

### Subtask

[OMOPRP-23](https://emoryrhsit.atlassian.net/browse/OMOPRP-23) person dupes

[OMOPRP-24](https://emoryrhsit.atlassian.net/browse/OMOPRP-24) measurement dupes

[OMOPRP-25](https://emoryrhsit.atlassian.net/browse/OMOPRP-25) care\_site dupes

[OMOPRP-26](https://emoryrhsit.atlassian.net/browse/OMOPRP-26) visit\_occurrence dupes