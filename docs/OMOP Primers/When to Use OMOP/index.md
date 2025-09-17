---
search:
  exclude: false
hide:
#   - toc
  - footer
title: When to Use OMOP
---

# When to Use OMOP

The following section outlines the key considerations for using the OMOP Common Data Model (CDM) at Emory vs. other resources available to us.

## Why OMOP Over Other Data Resources?

Enterprise OMOP is a structural standardization (all medications are in the Drug table) and semantic harmonization (all NDCs map to RxNorm) of the Clarity tables, as well as the legacy Clinical Data Warehouse primarily populated with Cerner data. All data are cleaned of non-events and mapped to standard codes where applicable. The OMOP vocabulary also contains hierarchies and classifications of coding systems enabling requestors to more easily define their variables of interest. The standardized structure and basic cleaning of the data enables easier query writing for BI-Developers.

## Use cases fit for Enterprise OMOP

The data in Enterprise OMOP meets many medical research use cases. Comparisions of different treatments on outcomes; healthcare utilization; EHR data linked to tumor registry participants; EHR data linked to OpenSpecimen biospecimen data. This data is focused on presenting the best information we have about what happened to the patient across a variety of data sources at Emory.

## Use Cases not fit for Enterprise OMOP

Enterprise OMOP is not a replacement for the Clarity tables, Caboodle Data Warehouse, or the Reporting Workbench. The Epic infrastructure is the source of truth for the ongoing data for our health care system ingested into an EHR. Enterprise OMOP is a cleaned and standardized version of the data in Legacy CDW and data in Epic Clarity. If you need to understand the full extent of what happened to a patient, you should always start with an Epic resource.

Data points which did not occur or are future events are not included. Examples include Canceled encounters, future procedures, missed medications, or charges for services. Historical demographic data for a patient or provider (i.e., deprecated or inaccurate demographics or addresses not representing current state) are not included.

Finally, the cadence for Enterprise OMOP at present is likely to lag behind real-time data and will never catch up to source (see “cadence” documentation for more). If a study is focused on the intent to treat (what care did the provider try to give and not what happened to the patient), then we should not use OMOP for that project.