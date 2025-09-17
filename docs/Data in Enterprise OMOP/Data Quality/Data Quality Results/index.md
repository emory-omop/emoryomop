# Overview

The following sections provide the specific tests per table, the overall pass (✅) or fail (❌) for the table in boolean terms, that roll up. That is, if ANY of the tests fail for the table, the roll-up to the table level fails, and the overall ETL fails. These results are current as of the release date at the top of this document. Within the yaml provided below, look for the tests as indicated by the `data_tests` key. The tests are run in the order they are listed, and if any of them fail, the table is marked as failed.

## Enterprise OMOP (✅)

### care_site (✅)

```yaml
models:
  - name: care_site
    description: The CARE_SITE table contains a list of uniquely identified institutional (physical or
      organizational) units where healthcare delivery is practiced (offices, wards, hospitals, clinics,
      etc.).
    columns:
      - name: care_site_id
        description: ''
        data_type: integer
      - name: care_site_name
        description: The name of the care_site as it appears in the source data
        data_type: varchar(255)
      - name: place_of_service_concept_id
        description: This is a high-level way of characterizing a Care Site. Typically,however, Care Sites
          can provide care in multiple settings (inpatient,outpatient, etc.) and this granularity should
          be reflected in the visit.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: place_of_service_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: location_id
        description: The location_id from the LOCATION table representing the physicallocation of the
          care_site.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('location')
              field: location_id
      - name: care_site_source_value
        description: The identifier of the care_site as it appears in the source data. Thiscould be an
          identifier separate from the name of the care_site.
        data_type: varchar(50)
      - name: place_of_service_source_value
        description: 'The place of service of the care_site as it appears in the source data'
        data_type: varchar(50)
```

### condition_occurrence (✅)

```yaml
models:
  - name: condition_occurrence
    description: This table contains records of Events of a Person suggesting the presence of a disease
      or medical condition stated as a diagnosis, a sign, or a symptom, which is either observed by a
      Provider or reported by the patient.
    columns:
      - name: condition_occurrence_id
        description: The unique key given to a condition record for a person. Refer to theETL for how
          duplicate conditions during the same visit were handled.
        data_type: integer
        ## Is not implemented in at this step in the ETL. Only applicable to final redshift instance
        # data_tests:
        #   - not_null
        #   - unique
      - name: person_id
        description: The PERSON_ID of the PERSON for whom the condition is recorded.
        data_type: integer
        data_tests:
          - not_null
          - relationships:
              to: ref('person')
              field: person_id
      - name: condition_concept_id
        description: The CONDITION_CONCEPT_ID field is recommended for primary use inanalyses, and must
          be used for network studies. This is the standardconcept mapped from the source value which
          represents a condition
        data_type: integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: condition_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Condition'
      - name: condition_start_date
        description: Use this date to determine the start date of the condition
        data_type: date
        data_tests:
          - not_null
      - name: condition_start_datetime
        description: ''
        data_type: datetime
      - name: condition_end_date
        description: Use this date to determine the end date of the condition
        data_type: date
      - name: condition_end_datetime
        description: ''
        data_type: datetime
      - name: condition_type_concept_id
        description: This field can be used to determine the provenance of the Conditionrecord, as in
          whether the condition was from an EHR system, insuranceclaim, registry, or other sources.
        data_type: integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: condition_type_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Type Concept'
      - name: condition_status_concept_id
        description: This concept represents the point during the visit the diagnosis wasgiven (admitting
          diagnosis, final diagnosis), whether the diagnosis wasdetermined due to laboratory findings,
          if the diagnosis wasexclusionary, or if it was a preliminary diagnosis, among others.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: condition_status_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Condition Status'
      - name: stop_reason
        description: The Stop Reason indicates why a Condition is no longer valid withrespect to the purpose
          within the source data. Note that a Stop Reasondoes not necessarily imply that the condition
          is no longer occurring.
        data_type: varchar(20)
      - name: provider_id
        description: The provider associated with condition record, e.g. the provider whomade the diagnosis
          or the provider who recorded the symptom.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('provider')
              field: provider_id
      - name: visit_occurrence_id
        description: The visit during which the condition occurred.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('visit_occurrence')
              field: visit_occurrence_id
      - name: visit_detail_id
        description: The VISIT_DETAIL record during which the condition occurred. Forexample, if the person
          was in the ICU at the time of the diagnosis theVISIT_OCCURRENCE record would reflect the overall
          hospital stay and theVISIT_DETAIL record would reflect the ICU stay during the hospitalvisit.
        data_type: integer
        ## Is not implemented in at this step in the ETL. Only applicable to final redshift instance
        # data_tests:
        #   - relationships:
        #       to: ref('visit_detail')
        #       field: visit_detail_id
      - name: condition_source_value
        description: This field houses the verbatim value from the source data representingthe condition
          that occurred. For example, this could be an ICD10 or Readcode.
        data_type: varchar(50)
      - name: condition_source_concept_id
        description: This is the concept representing the condition source value and may notnecessarily
          be standard. This field is discouraged from use in analysisbecause it is not required to contain
          Standard Concepts that are usedacross the OHDSI community, and should only be used when StandardConcepts
          do not adequately represent the source detail for the Conditionnecessary for a given analytic
          use case. Consider usingCONDITION_CONCEPT_ID instead to enable standardized analytics that canbe
          consistent across the network.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: condition_source_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: condition_status_source_value
        description: This field houses the verbatim value from the source data representingthe condition
          status.
        data_type: varchar(50)
```

### death (✅)

```yaml
models:
  - name: death
    description: 'The death domain contains the clinical event for how and when a Person dies. A person
      can have up to one record if the source system contains evidence about the Death, such as: Condition
      in an administrative claim, status of enrollment into a health plan, or explicit record in EHR data.'
    columns:
      - name: person_id
        description: ''
        data_type: integer
        data_tests:
          - not_null
          - relationships:
              to: ref('person')
              field: person_id
      - name: death_date
        description: The date the person was deceased.
        data_type: date
        data_tests:
          - not_null
      - name: death_datetime
        description: ''
        data_type: datetime
      - name: death_type_concept_id
        description: This is the provenance of the death record, i.e., where it came from. Itis possible
          that an administrative claims database would source deathinformation from a government file
          so do not assume the Death Type isthe same as the Visit Type, etc.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: death_type_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Type Concept'
      - name: cause_concept_id
        description: This is the Standard Concept representing the Person's cause of death,if available.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: cause_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: cause_source_value
        description: ''
        data_type: varchar(50)
      - name: cause_source_concept_id
        description: ''
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: cause_source_concept_id not in (0, -99) # Emory Specific for "flavor" of null
```

### device_exposure (✅)

```yaml
models:
  - name: device_exposure
    description: The Device domain captures information about a person's exposure to a foreign physical
      object or instrument which is used for diagnostic or therapeutic purposes through a mechanism beyond
      chemical action. Devices include implantable objects (e.g. pacemakers, stents, artificial joints),
      medical equipment and supplies (e.g. bandages, crutches, syringes), other instruments used in medical
      procedures (e.g. sutures, defibrillators) and material used in clinical care (e.g. adhesives, body
      material, dental material, surgical material).
    columns:
      - name: device_exposure_id
        description: The unique key given to records a person's exposure to a foreignphysical object or
          instrument.
        data_type: integer
        ## Is not implemented in at this step in the ETL. Only applicable to final redshift instance
        # tests:
        #   - not_null
        #   - unique
      - name: person_id
        description: ''
        data_type: integer
        data_tests:
          - not_null
          - relationships:
              to: ref('person')
              field: person_id
      - name: device_concept_id
        description: The DEVICE_CONCEPT_ID field is recommended for primary use in analyses,and must be
          used for network studies. This is the standard conceptmapped from the source concept id which
          represents a foreign object orinstrument the person was exposed to.
        data_type: integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: device_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Device'
      - name: device_exposure_start_date
        description: Use this date to determine the start date of the device record.
        data_type: date
        data_tests:
          - not_null
      - name: device_exposure_start_datetime
        description: ''
        data_type: datetime
      - name: device_exposure_end_date
        description: The DEVICE_EXPOSURE_END_DATE denotes the day the device exposure endedfor the patient,
          if given.
        data_type: date
      - name: device_exposure_end_datetime
        description: ''
        data_type: datetime
      - name: device_type_concept_id
        description: You can use the TYPE_CONCEPT_ID to denote the provenance of the record,as in whether
          the record is from administrative claims or EHR.
        data_type: integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: device_type_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Type Concept'
      - name: unique_device_id
        description: This is the Unique Device Identification (UDI-DI) number for devicesregulated by
          the FDA, if given.
        data_type: varchar(255)
      - name: production_id
        description: This is the Production Identifier (UDI-PI) portion of the Unique DeviceIdentification.
        data_type: varchar(255)
      - name: quantity
        description: ''
        data_type: integer
      - name: provider_id
        description: The Provider associated with device record, e.g. the provider who wrotethe prescription
          or the provider who implanted the device.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('provider')
              field: provider_id
      - name: visit_occurrence_id
        description: The Visit during which the device was prescribed or given.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('visit_occurrence')
              field: visit_occurrence_id
      - name: visit_detail_id
        description: The Visit Detail during which the device was prescribed or given.
        data_type: integer
        ## Is not implemented in at this step in the ETL. Only applicable to final redshift instance
        # tests:
        #   - relationships:
        #       to: ref('visit_detail')
        #       field: visit_detail_id
      - name: device_source_value
        description: This field houses the verbatim value from the source data representingthe device
          exposure that occurred. For example, this could be an NDC orGemscript code.
        data_type: varchar(50)
      - name: device_source_concept_id
        description: This is the concept representing the device source value and may notnecessarily be
          standard. This field is discouraged from use in analysisbecause it is not required to contain
          Standard Concepts that are usedacross the OHDSI community, and should only be used when StandardConcepts
          do not adequately represent the source detail for the Devicenecessary for a given analytic use
          case. Consider usingDEVICE_CONCEPT_ID instead to enable standardized analytics that can beconsistent
          across the network.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: device_source_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: unit_concept_id
        description: UNIT_SOURCE_VALUES should be mapped to a Standard Concept in the Unitdomain that
          best represents the unit as given in the source data.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: unit_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Unit'
      - name: unit_source_value
        description: This field houses the verbatim value from the source data representingthe unit of
          the Device. For example, blood transfusions are considereddevices and can be given in mL quantities.
        data_type: varchar(50)
      - name: unit_source_concept_id
        description: This is the concept representing the UNIT_SOURCE_VALUE and may notnecessarily be
          standard. This field is discouraged from use in analysisbecause it is not required to contain
          Standard Concepts that are usedacross the OHDSI community, and should only be used when StandardConcepts
          do not adequately represent the source detail for the Unitnecessary for a given analytic use
          case. Consider using UNIT_CONCEPT_IDinstead to enable standardized analytics that can be consistent
          acrossthe network.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: unit_source_concept_id not in (0, -99) # Emory Specific for "flavor" of null
```

### drug_exposure (✅)

```yaml
models:
  - name: drug_exposure
    description: This table captures records about the exposure to a Drug ingested or otherwise introduced
      into the body. A Drug is a biochemical substance formulated in such a way that when administered
      to a Person it will exert a certain biochemical effect on the metabolism. Drugs include prescription
      and over-the-counter medicines, vaccines, and large-molecule biologic therapies. Radiological devices
      ingested or applied locally do not count as Drugs.
    columns:
      - name: drug_exposure_id
        description: The unique key given to records of drug dispensings or administrationsfor a person.
          Refer to the ETL for how duplicate drugs during the samevisit were handled.
        data_type: integer
        ## Is not implemented in at this step in the ETL. Only applicable to final redshift instance
        # tests:
        #   - not_null
        #   - unique
      - name: person_id
        description: The PERSON_ID of the PERSON for whom the drug dispensing oradministration is recorded.
          This may be a system generated code.
        data_type: integer
        data_tests:
          - not_null
          - relationships:
              to: ref('person')
              field: person_id
      - name: drug_concept_id
        description: The DRUG_CONCEPT_ID field is recommended for primary use in analyses,and must be
          used for network studies. This is the standard conceptmapped from the source concept id which
          represents a drug product ormolecule otherwise introduced to the body. The drug concepts can
          have avarying degree of information about drug strength and dose. Thisinformation is relevant
          in the context of quantity and administrationinformation in the subsequent fields plus strength
          information from theDRUG_STRENGTH table, provided as part of the standard vocabularydownload.
        data_type: integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: drug_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Drug'
      - name: drug_exposure_start_date
        description: Use this date to determine the start date of the drug record.
        data_type: date
        data_tests:
          - not_null
      - name: drug_exposure_start_datetime
        description: ''
        data_type: datetime
      - name: drug_exposure_end_date
        description: The DRUG_EXPOSURE_END_DATE denotes the day the drug exposure ended forthe patient.
        data_type: date
        data_tests:
          - not_null
      - name: drug_exposure_end_datetime
        description: ''
        data_type: datetime
      - name: verbatim_end_date
        description: This is the end date of the drug exposure as it appears in the sourcedata, if it
          is given
        data_type: date
      - name: drug_type_concept_id
        description: You can use the TYPE_CONCEPT_ID to delineate between prescriptionswritten vs. prescriptions
          dispensed vs. medication historyvs. patient-reported exposure, etc.
        data_type: integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: drug_type_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Type Concept'
      - name: stop_reason
        description: The reason a person stopped a medication as it is represented in thesource. Reasons
          include regimen completed, changed, removed, etc. Thisfield will be retired in v6.0.
        data_type: varchar(20)
      - name: refills
        description: This is only filled in when the record is coming from a prescriptionwritten this
          field is meant to represent intended refills at time of theprescription.
        data_type: integer
      - name: quantity
        description: ''
        data_type: float
      - name: days_supply
        description: ''
        data_type: integer
      - name: sig
        description: This is the verbatim instruction for the drug as written by theprovider.
        data_type: varchar(MAX)
      - name: route_concept_id
        description: ''
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: route_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Route'
      - name: lot_number
        description: ''
        data_type: varchar(50)
      - name: provider_id
        description: The Provider associated with drug record, e.g. the provider who wrotethe prescription
          or the provider who administered the drug.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('provider')
              field: provider_id
      - name: visit_occurrence_id
        description: The Visit during which the drug was prescribed, administered ordispensed.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('visit_occurrence')
              field: visit_occurrence_id
      - name: visit_detail_id
        description: The VISIT_DETAIL record during which the drug exposure occurred. Forexample, if the
          person was in the ICU at the time of the drugadministration the VISIT_OCCURRENCE record would
          reflect the overallhospital stay and the VISIT_DETAIL record would reflect the ICU stayduring
          the hospital visit.
        data_type: integer
        ## Is not implemented in at this step in the ETL. Only applicable to final redshift instance
        # tests:
        #   - relationships:
        #       to: ref('visit_detail')
        #       field: visit_detail_id
      - name: drug_source_value
        description: This field houses the verbatim value from the source data representingthe drug exposure
          that occurred. For example, this could be an NDC orGemscript code.
        data_type: varchar(50)
      - name: drug_source_concept_id
        description: This is the concept representing the drug source value and may notnecessarily be
          standard. This field is discouraged from use in analysisbecause it is not required to contain
          Standard Concepts that are usedacross the OHDSI community, and should only be used when StandardConcepts
          do not adequately represent the source detail for the Drugnecessary for a given analytic use
          case. Consider using DRUG_CONCEPT_IDinstead to enable standardized analytics that can be consistent
          acrossthe network.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: drug_source_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: route_source_value
        description: This field houses the verbatim value from the source data representingthe drug route.
        data_type: varchar(50)
      - name: dose_unit_source_value
        description: This field houses the verbatim value from the source data representingthe dose unit
          of the drug given.
        data_type: varchar(50)
```

### location (✅)

```yaml
models:
  - name: location
    description: The LOCATION table represents a generic way to capture physical location or address information
      of Persons and Care Sites.
    columns:
      - name: location_id
        description: The unique key given to a unique Location.
        data_type: integer
        data_tests:
          - not_null
          - unique
      - name: address_1
        description: This is the first line of the address.
        data_type: varchar(50)
      - name: address_2
        description: This is the second line of the address
        data_type: varchar(50)
      - name: city
        description: ''
        data_type: varchar(50)
      - name: state
        description: ''
        data_type: varchar(2)
      - name: zip
        description: ''
        data_type: varchar(9)
      - name: county
        description: ''
        data_type: varchar(20)
      - name: location_source_value
        description: ''
        data_type: varchar(50)
      - name: country_concept_id
        description: The Concept Id representing the country. Values should conform to the Geographydomain.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: country_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: country_source_value
        description: The name of the country.
        data_type: varchar(80)
      - name: latitude
        description: ''
        data_type: double
      - name: longitude
        description: ''
        data_type: double
```

### measurement (✅)

```yaml
models:
  - name: measurement
    description: The MEASUREMENT table contains records of Measurements, i.e. structured values (numerical
      or categorical) obtained through systematic and standardized examination or testing of a Person
      or Person's sample. The MEASUREMENT table contains both orders and results of such Measurements
      as laboratory tests, vital signs, quantitative findings from pathology reports, etc. Measurements
      are stored as attribute value pairs, with the attribute as the Measurement Concept and the value
      representing the result. The value can be a Concept (stored in VALUE_AS_CONCEPT), or a numerical
      value (VALUE_AS_NUMBER) with a Unit (UNIT_CONCEPT_ID). The Procedure for obtaining the sample is
      housed in the PROCEDURE_OCCURRENCE table, though it is unnecessary to create a PROCEDURE_OCCURRENCE
      record for each measurement if one does not exist in the source data. Measurements differ from Observations
      in that they require a standardized test or some other activity to generate a quantitative or qualitative
      result. If there is no result, it is assumed that the lab test was conducted but the result was
      not captured.
    columns:
      - name: measurement_id
        description: The unique key given to a Measurement record for a Person. Refer to theETL for how
          duplicate Measurements during the same Visit were handled.
        data_type: integer
        ## Is not implemented in at this step in the ETL. Only applicable to final redshift instance
        # tests:
        #   - not_null
        #   - unique
      - name: person_id
        description: The PERSON_ID of the Person for whom the Measurement is recorded. Thismay be a system
          generated code.
        data_type: integer
        data_tests:
          - not_null
          - relationships:
              to: ref('person')
              field: person_id
      - name: measurement_concept_id
        description: The MEASUREMENT_CONCEPT_ID field is recommended for primary use inanalyses, and must
          be used for network studies. This is the standardconcept mapped from the source value which
          represents a measurement.
        data_type: integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: measurement_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Measurement'
      - name: measurement_date
        description: Use this date to determine the date of the measurement.
        data_type: date
        data_tests:
          - not_null
      - name: measurement_datetime
        description: ''
        data_type: datetime
      - name: measurement_time
        description: ''
        data_type: varchar(10)
      - name: measurement_type_concept_id
        description: This field can be used to determine the provenance of the Measurementrecord, as in
          whether the measurement was from an EHR system, insuranceclaim, registry, or other sources.
        data_type: integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: measurement_type_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Type Concept'
      - name: operator_concept_id
        description: The meaning of Concept 4172703for '=' is identical to omission of a OPERATOR_CONCEPT_ID
          value. Sincethe use of this field is rare, it's important when devising analyses tonot to forget
          testing for the content of this field for values differentfrom =.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: operator_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: value_as_number
        description: This is the numerical value of the Result of the Measurement, ifavailable. Note that
          measurements such as blood pressures will be split into their component parts i.e. one record
          for systolic, one record fordiastolic.
        data_type: float
      - name: value_as_concept_id
        description: If the raw data gives a categorial result for measurements those valuesare captured
          and mapped to standard concepts in the 'Meas Value' domain.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: value_as_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: unit_concept_id
        description: At present, there isn't a prescribed unit for individual measurements,such as Hemoglobin
          A1C, meaning it's not obligatory to express thesemeasurements as a percentage. UNIT_SOURCE_VALUES
          should be linked to aStandard Concept within the Unit domain that most accurately reflectsthe
          unit provided in the source data.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: unit_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Unit'
      - name: range_low
        description: Ranges have the same unit as the VALUE_AS_NUMBER. These ranges areprovided by the
          source and should remain NULL if not given.
        data_type: float
      - name: range_high
        description: Ranges have the same unit as the VALUE_AS_NUMBER. These ranges areprovided by the
          source and should remain NULL if not given.
        data_type: float
      - name: provider_id
        description: The provider associated with measurement record, e.g. the provider who ordered the
          test or the provider who recorded the result.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('provider')
              field: provider_id
      - name: visit_occurrence_id
        description: The visit during which the Measurement occurred.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('visit_occurrence')
              field: visit_occurrence_id
      - name: visit_detail_id
        description: The VISIT_DETAIL record during which the Measurement occurred. Forexample, if the
          Person was in the ICU at the time the VISIT_OCCURRENCErecord would reflect the overall hospital
          stay and the VISIT_DETAILrecord would reflect the ICU stay during the hospital visit.
        data_type: integer
        ## Is not implemented in at this step in the ETL. Only applicable to final redshift instance
        # tests:
        #   - relationships:
        #       to: ref('visit_detail')
        #       field: visit_detail_id
      - name: measurement_source_value
        description: This field contains the exact value from the source data that representsthe measurement
          that occurred.
        data_type: varchar(50)
      - name: measurement_source_concept_id
        description: This is the concept representing the MEASUREMENT_SOURCE_VALUE and maynot necessarily
          be standard. This field is discouraged from use inanalysis because it is not required to contain
          Standard Concepts thatare used across the OHDSI community, and should only be used whenStandard
          Concepts do not adequately represent the source detail for theMeasurement necessary for a given
          analytic use case. Consider usingMEASUREMENT_CONCEPT_ID instead to enable standardized analytics
          that canbe consistent across the network.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: measurement_source_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: unit_source_value
        description: This field contains the exact value from the source data that representsthe unit
          of measurement used.
        data_type: varchar(50)
      - name: unit_source_concept_id
        description: “This is the concept representing the UNIT_SOURCE_VALUE and may notnecessarily be
          standard. This field is discouraged from use in analysisbecause it is not required to contain
          Standard Concepts that are usedacross the OHDSI community, and should only be used when StandardConcepts
          do not adequately represent the source detail for theMeasurement necessary for a given analytic
          use case. Consider usingUNIT_CONCEPT_ID instead to enable standardized analytics that can beconsistent
          across the network.”
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: unit_source_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: value_source_value
        description: This field houses the verbatim result value of the Measurement from thesource data
          .
        data_type: varchar(50)
      - name: measurement_event_id
        description: If the Measurement record is related to another record in the database,this field
          is the primary key of the linked record.
        data_type: integer
      - name: meas_event_field_concept_id
        description: If the Measurement record is related to another record in the database,this field
          is the CONCEPT_ID that identifies which table the primary keyof the linked record came from.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: meas_event_field_concept_id not in (0, -99) # Emory Specific for "flavor" of null
```

### observation (✅)

```yaml
models:
  - name: observation
    description: The OBSERVATION table captures clinical facts about a Person obtained in the context
      of examination, questioning or a procedure. Any data that cannot be represented by any other domains,
      such as social and lifestyle facts, medical history, family history, etc. are recorded here.
    columns:
      - name: observation_id
        description: The unique key given to an Observation record for a Person. Refer to the ETL for how
          duplicate Observations during the same Visit were handled.
        data_type: integer
        ## Is not implemented in at this step in the ETL. Only applicable to final redshift instance
        # tests:
        #   - not_null
        #   - unique
      - name: person_id
        description: The PERSON_ID of the Person for whom the Observation is recorded. Thismay be a system
          generated code.
        data_type: integer
        data_tests:
          - not_null
          - relationships:
              to: ref('person')
              field: person_id
      - name: observation_concept_id
        description: The OBSERVATION_CONCEPT_ID field is recommended for primary use in analyses, and must
          be used for network studies.
        data_type: integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: observation_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: observation_date
        description: The date of when the Observation was obtained. Depending on what the Observation represents
          this could be the date of a lab test, the date ofa survey, or the date a patient's family history
          was taken.
        data_type: date
        data_tests:
          - not_null
      - name: observation_datetime
        description: ''
        data_type: datetime
      - name: observation_type_concept_id
        description: This field can be used to determine the provenance of the Observation record, as in
          whether the measurement was from an EHR system, insuranceclaim, registry, or other sources.
        data_type: integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: observation_type_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Type Concept'
      - name: value_as_number
        description: This is the numerical value of the Result of the Observation, if applicable and available.
          It is not expected that all Observations willhave numeric results, rather, this field is here
          to house values shouldthey exist.
        data_type: float
      - name: value_as_string
        description: This is the categorical value of the Result of the Observation, if applicable and
          available.
        data_type: varchar(60)
      - name: value_as_concept_id
        description: It is possible that some records destined for the Observation table havetwo clinical
          ideas represented in one source code. This is common with ICD10 codes that describe a family
          history of some Condition, for example. In OMOP the Vocabulary breaks these two clinical ideas
          into two codes; one becomes the OBSERVATION_CONCEPT_ID and the other becomes the VALUE_AS_CONCEPT_ID.
          It is important when using the Observation table tokeep this possibility in mind and to examine
          the VALUE_AS_CONCEPT_IDfield for relevant information.
        data_type: Integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: value_as_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: qualifier_concept_id
        description: This field contains all attributes specifying the clinical fact further,such as as
          degrees, severities, drug-drug interaction alerts etc.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: qualifier_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: unit_concept_id
        description: There is currently no recommended unit for individual observation concepts. UNIT_SOURCE_VALUES
          should be mapped to a Standard Concept in the Unit domain that best represents the unit as given
          in the sourcedata.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: unit_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Unit'
      - name: provider_id
        description: The provider associated with the observation record, e.g. the provider who ordered
          the test or the provider who recorded the result.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('provider')
              field: provider_id
      - name: visit_occurrence_id
        description: The visit during which the Observation occurred.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('visit_occurrence')
              field: visit_occurrence_id
      - name: visit_detail_id
        description: The VISIT_DETAIL record during which the Observation occurred. Forexample, if the
          Person was in the ICU at the time the VISIT_OCCURRENCErecord would reflect the overall hospital
          stay and the VISIT_DETAILrecord would reflect the ICU stay during the hospital visit.
        data_type: integer
        ## Is not implemented in at this step in the ETL. Only applicable to final redshift instance
        # tests:
        #   - relationships:
        #       to: ref('visit_detail')
        #       field: visit_detail_id
      - name: observation_source_value
        description: This field houses the verbatim value from the source data representingthe Observation
          that occurred. For example, this could be an ICD10 orRead code.
        data_type: varchar(50)
      - name: observation_source_concept_id
        description: This is the concept representing the OBSERVATION_SOURCE_VALUE and maynot necessarily
          be standard. This field is discouraged from use in analysis because it is not required to contain
          Standard Concepts thatare used across the OHDSI community, and should only be used whenStandard
          Concepts do not adequately represent the source detail for theObservation necessary for a given
          analytic use case. Consider using OBSERVATION_CONCEPT_ID instead to enable standardized analytics
          that canbe consistent across the network.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: observation_source_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: unit_source_value
        description: This field houses the verbatim value from the source data representingthe unit of
          the Observation that occurred.
        data_type: varchar(50)
      - name: qualifier_source_value
        description: This field houses the verbatim value from the source data representingthe qualifier
          of the Observation that occurred.
        data_type: varchar(50)
      - name: value_source_value
        description: This field houses the verbatim result value of the Observation from thesource data.
          Do not get confused with the Observation_source_value which captures source value of the observation
          mapped to observation_concept_id. This field is the observation result value from the source.
        data_type: varchar(50)
      - name: observation_event_id
        description: If the Observation record is related to another record in the database,this field
          is the primary key of the linked record.
        data_type: integer
      - name: obs_event_field_concept_id
        description: If the Observation record is related to another record in the database,this field
          is the CONCEPT_ID that identifies which table the primary keyof the linked record came from.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: obs_event_field_concept_id not in (0, -99) # Emory Specific for "flavor" of null
```

### person (✅)

```yaml
models:
  - name: person
    description: This table serves as the central identity management for all Persons in the database.
      It contains records that uniquely identify each person or patient, and some demographic information.
    columns:
      - name: person_id
        description: It is assumed that every person with a different unique identifier is infact a different
          person and should be treated independently.
        data_type: integer
        data_tests:
          - not_null
          - unique
      - name: gender_concept_id
        description: This field is meant to capture the biological sex at birth of the Person. This field
          should not be used to study gender identity issues.
        data_type: integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: gender_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Gender'
      - name: year_of_birth
        description: Compute age using year_of_birth.
        data_type: integer
        data_tests:
          - not_null
      - name: month_of_birth
        description: ''
        data_type: integer
      - name: day_of_birth
        description: ''
        data_type: integer
      - name: birth_datetime
        description: ''
        data_type: datetime
      - name: race_concept_id
        description: This field captures race or ethnic background of the person.
        data_type: integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: race_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Race'
      - name: ethnicity_concept_id
        description: 'This field captures Ethnicity as defined by the Office of Management and Budget (OMB)
          of the US Government: it distinguishes only between “Hispanic” and “Not Hispanic”. Races and
          ethnic backgrounds are notstored here.'
        data_type: integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: ethnicity_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Ethnicity'
      - name: location_id
        description: The location refers to the physical address of the person. This field should capture
          the last known location of the person.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('location')
              field: location_id
      - name: provider_id
        description: The Provider refers to the last known primary care provider (GeneralPractitioner).
        data_type: integer
        data_tests:
          - relationships:
              to: ref('provider')
              field: provider_id
      - name: care_site_id
        description: The Care Site refers to where the Provider typically provides the primary care.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('care_site')
              field: care_site_id
      - name: person_source_value
        description: Use this field to link back to persons in the source data. This is typically used
          for error checking of ETL logic.
        data_type: varchar(50)
      - name: gender_source_value
        description: This field is used to store the biological sex of the person from the source data.
          It is not intended for use in standard analytics but forreference only.
        data_type: varchar(50)
      - name: gender_source_concept_id
        description: Due to the small number of options, this tends to be zero.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: gender_source_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: race_source_value
        description: This field is used to store the race of the person from the source data. It is not
          intended for use in standard analytics but for reference only.
        data_type: varchar(50)
      - name: race_source_concept_id
        description: Due to the small number of options, this tends to be zero.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: race_source_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: ethnicity_source_value
        description: This field is used to store the ethnicity of the person from the source data. It is
          not intended for use in standard analytics but for referenceonly.
        data_type: varchar(50)
      - name: ethnicity_source_concept_id
        description: Due to the small number of options, this tends to be zero.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: ethnicity_source_concept_id not in (0, -99) # Emory Specific for "flavor" of null
```

### procedure_occurrence (✅)

```yaml
models:
  - name: procedure_occurrence
    description: This table contains records of activities or processes ordered by, or carried out by,
      a healthcare provider on the patient with a diagnostic or therapeutic purpose.
    columns:
      - name: procedure_occurrence_id
        description: The unique key given to a procedure record for a person. Refer to theETL for how
          duplicate procedures during the same visit were handled.
        data_type: integer
        ## Is not implemented in at this step in the ETL. Only applicable to final redshift instance
        # tests:
        #   - not_null
        #   - unique
      - name: person_id
        description: The PERSON_ID of the PERSON for whom the procedure is recorded. This maybe a system
          generated code.
        data_type: integer
        data_tests:
          - not_null
          - relationships:
              to: ref('person')
              field: person_id
      - name: procedure_concept_id
        description: The PROCEDURE_CONCEPT_ID field is recommended for primary use inanalyses, and must
          be used for network studies. This is the standardconcept mapped from the source value which
          represents a procedure
        data_type: integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: procedure_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Procedure'
      - name: procedure_date
        description: Use this date to determine the date the procedure started.
        data_type: date
        data_tests:
          - not_null
      - name: procedure_datetime
        description: ''
        data_type: datetime
      - name: procedure_end_date
        description: Use this field to house the date that the procedure ended.
        data_type: date
      - name: procedure_end_datetime
        description: Use this field to house the datetime that the procedure ended.
        data_type: datetime
      - name: procedure_type_concept_id
        description: This field can be used to determine the provenance of the Procedurerecord, as in
          whether the procedure was from an EHR system, insuranceclaim, registry, or other sources.
        data_type: integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: procedure_type_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Type Concept'
      - name: modifier_concept_id
        description: The modifiers are intended to give additional information about theprocedure but
          as of now the vocabulary is under review.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: modifier_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: quantity
        description: If the quantity value is omitted, a single procedure is assumed.
        data_type: integer
      - name: provider_id
        description: The provider associated with the procedure record, e.g. the provider whoperformed
          the Procedure.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('provider')
              field: provider_id
      - name: visit_occurrence_id
        description: The visit during which the procedure occurred.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('visit_occurrence')
              field: visit_occurrence_id
      - name: visit_detail_id
        description: The VISIT_DETAIL record during which the Procedure occurred. Forexample, if the Person
          was in the ICU at the time of the Procedure theVISIT_OCCURRENCE record would reflect the overall
          hospital stay and theVISIT_DETAIL record would reflect the ICU stay during the hospitalvisit.
        data_type: integer
        ## Is not implemented in at this step in the ETL. Only applicable to final redshift instance
        # tests:
        #   - relationships:
        #       to: ref('visit_detail')
        #       field: visit_detail_id
      - name: procedure_source_value
        description: This field houses the verbatim value from the source data representingthe procedure
          that occurred. For example, this could be an CPT4 or OPCS4code.
        data_type: varchar(50)
      - name: procedure_source_concept_id
        description: This is the concept representing the procedure source value and may notnecessarily
          be standard. This field is discouraged from use in analysisbecause it is not required to contain
          Standard Concepts that are usedacross the OHDSI community, and should only be used when StandardConcepts
          do not adequately represent the source detail for the Procedurenecessary for a given analytic
          use case. Consider usingPROCEDURE_CONCEPT_ID instead to enable standardized analytics that canbe
          consistent across the network.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: procedure_source_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: modifier_source_value
        description: This field houses the verbatim value from the source data representingthe modifier
          code for the procedure that occurred.
        data_type: varchar(50)
```

### provider (✅)

```yaml
models:
  - name: provider
    description: The PROVIDER table contains a list of uniquely identified healthcare providers; duplication
      is not allowed. These are individuals providing hands-on healthcare to patients, such as physicians,
      nurses, midwives, physical therapists etc.
    columns:
      - name: provider_id
        description: It is assumed that every provider with a different unique identifier isin fact a
          different person and should be treated independently.
        data_type: integer
        data_tests:
          - not_null
          - unique
      - name: provider_name
        description: This field contains information that describes a healthcare provider.
        data_type: varchar(255)
      - name: npi
        description: This is the National Provider Number issued to health care providers inthe US by
          the Centers for Medicare and Medicaid Services (CMS).
        data_type: varchar(20)
      - name: dea
        description: This is the identifier issued by the DEA, a US federal agency, thatallows a provider
          to write prescriptions for controlled substances.
        data_type: varchar(20)
      - name: specialty_concept_id
        description: This field either represents the most common specialty that occurs inthe data or
          the most specific Concept that represents all specialtieslisted, should the provider have more
          than one. This includes physicianspecialties such as internal medicine, emergency medicine,
          etc. andallied health professionals such as nurses, midwives, and pharmacists.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: specialty_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: care_site_id
        description: This is the CARE_SITE_ID for the location that the provider primarilypractices in.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('care_site')
              field: care_site_id
              from_condition: care_site_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: year_of_birth
        description: ''
        data_type: integer
      ## Not implemented at this time (4/16/2025)
      # - name: gender_concept_id
      #   description: This field represents the recorded gender of the provider in the sourcedata.
      #   data_type: integer
      #   tests:
      #     - dbt_utils.relationships_where:
      #         to: ref('concept')
      #         field: concept_id
      #         from_condition: gender_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      #         to_condition: domain_id = 'Gender'
      - name: provider_source_value
        description: Use this field to link back to providers in the source data. This istypically used
          for error checking of ETL logic.
        data_type: varchar(50)
      - name: specialty_source_value
        description: This refers to the specific type of healthcare provider or field ofexpertise listed
          in the source data, encompassing physician specialtieslike internal medicine, emergency medicine,
          etc., as well as alliedhealth professionals such as nurses, midwives, and pharmacists. Itcovers
          medical specialties like surgery, internal medicine, andradiology, while other services like
          prosthetics, acupuncture, andphysical therapy fall under the domain of “Service.”
        data_type: varchar(50)
      - name: specialty_source_concept_id
        description: This is often zero as many sites use proprietary codes to storephysician speciality.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: specialty_source_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: gender_source_value
        description: This is provider's gender as it appears in the source data.
        data_type: varchar(50)
      - name: gender_source_concept_id
        description: This is often zero as many sites use proprietary codes to store providergender.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: gender_source_concept_id not in (0, -99) # Emory Specific for "flavor" of null
```

### visit_detail (✅)

```yaml
models:
  - name: visit_detail
    description: The VISIT_DETAIL table is an optional table used to represents details of each record
      in the parent VISIT_OCCURRENCE table. A good example of this would be the movement between units
      in a hospital during an inpatient stay or claim lines associated with a one insurance claim. For
      every record in the VISIT_OCCURRENCE table there may be 0 or more records in the VISIT_DETAIL table
      with a 1:n relationship where n may be 0. The VISIT_DETAIL table is structurally very similar to
      VISIT_OCCURRENCE table and belongs to the visit domain.
    columns:
      - name: visit_detail_id
        description: Use this to identify unique interactions between a person and the healthcare system.
          This identifier links across the other CDM event tables toassociate events with a visit detail.
        data_type: integer
        ## Is not implemented in at this step in the ETL. Only applicable to final redshift instance
        # tests:
        #   - not_null
        #   - unique
      - name: person_id
        description: ''
        data_type: integer
        data_tests:
          - not_null
          - relationships:
              to: ref('person')
              field: person_id
      - name: visit_detail_concept_id
        description: This field contains a concept id representing the kind of visit detail,like inpatient
          or outpatient. All concepts in this field should bestandard and belong to the Visit domain.
        data_type: integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: visit_detail_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Visit'
      - name: visit_detail_start_date
        description: This is the date of the start of the encounter. This may or may not beequal to the
          date of the Visit the Visit Detail is associated with.
        data_type: date
        data_tests:
          - not_null
      - name: visit_detail_start_datetime
        description: ''
        data_type: datetime
      - name: visit_detail_end_date
        description: This the end date of the patient-provider interaction. If a Person isstill an inpatient
          in the hospital at the time of the data extract anddoes not have a visit_end_date, then set
          the visit_end_date to the dateof the data pull.
        data_type: date
        data_tests:
          - not_null
      - name: visit_detail_end_datetime
        description: If a Person is still an inpatient in the hospital at the time of thedata extract
          and does not have a visit_end_datetime, then set thevisit_end_datetime to the datetime of the
          data pull.
        data_type: datetime
      - name: visit_detail_type_concept_id
        description: Use this field to understand the provenance of the visit detail record,or where the
          record comes from.
        data_type: integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: visit_detail_type_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Type Concept'
      - name: provider_id
        description: There will only be one provider per visit record andthe ETL document should clearly
          state how they were chosen (attending,admitting, etc.). This is a typical reason for leveraging
          theVISIT_DETAIL table as even though each VISIT_DETAIL record can only haveone provider, there
          is no limit to the number of VISIT_DETAIL recordsthat can be associated to a VISIT_OCCURRENCE
          record.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('provider')
              field: provider_id
      - name: care_site_id
        description: This field provides information about the Care Site where the VisitDetail took place.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('care_site')
              field: care_site_id
      - name: visit_detail_source_value
        description: This field houses the verbatim value from the source data representingthe kind of
          visit detail that took place (inpatient, outpatient,emergency, etc.)
        data_type: varchar(50)
      - name: visit_detail_source_concept_id
        description: ''
        data_type: Integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: visit_detail_source_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: admitted_from_concept_id
        description: Use this field to determine where the patient was admitted from. Thisconcept is part
          of the visit domain and can indicate if a patient wasadmitted to the hospital from a long-term
          care facility, for example.
        data_type: Integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: admitted_from_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Visit'
      - name: admitted_from_source_value
        description: ''
        data_type: varchar(50)
      - name: discharged_to_source_value
        description: ''
        data_type: varchar(50)
      - name: discharged_to_concept_id
        description: Use this field to determine where the patient was discharged to after avisit. This
          concept is part of the visit domain and can indicate if apatient was transferred to another
          hospital or sent to a long-term carefacility, for example. It is assumed that a person is discharged
          to hometherefore there is not a standard concept id for “home”. Use concept id= 0 when a person
          is discharged to home.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: discharged_to_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Visit'
      - name: preceding_visit_detail_id
        description: Use this field to find the visit detail that occurred for the personprior to the
          given visit detail record. There could be a few days or afew years in between.
        data_type: integer
        ## Is not implemented in at this step in the ETL. Only applicable to final redshift instance
        # tests:
        #   - relationships:
        #       to: ref('visit_detail')
        #       field: visit_detail_id
      - name: parent_visit_detail_id
        description: Use this field to find the visit detail that subsumes the given visitdetail record.
          This is used in the case that a visit detail record needsto be nested beyond the VISIT_OCCURRENCE/VISIT_DETAIL
          relationship.
        data_type: integer
        ## Is not implemented in at this step in the ETL. Only applicable to final redshift instance
        # tests:
        #   - relationships:
        #       to: ref('visit_detail')
        #       field: visit_detail_id
      - name: visit_occurrence_id
        description: Use this field to link the VISIT_DETAIL record to its VISIT_OCCURRENCE.
        data_type: integer
        # tests:
        #   - not_null
        #   - relationships:
        #       to: ref('visit_occurrence')
        #       field: visit_occurrence_id
```

### visit_occurrence (✅)

```yaml
models:
  - name: visit_occurrence
    description: This table contains Events where Persons engage with the healthcare system for a duration
      of time. They are often also called “Encounters”. Visits are defined by a configuration of circumstances
      under which they occur, such as (i) whether the patient comes to a healthcare institution, the other
      way around, or the interaction is remote, (ii) whether and what kind of trained medical staff is
      delivering the service during the Visit, and (iii) whether the Visit is transient or for a longer
      period involving a stay in bed.
    columns:
      - name: visit_occurrence_id
        description: Use this to identify unique interactions between a person and the healthcare system.
          This identifier links across the other CDM event tables toassociate events with a visit.
        data_type: integer
        ## Is not implemented in at this step in the ETL. Only applicable to final redshift instance
        # tests:
        #   - not_null
        #   - unique
      - name: person_id
        description: ''
        data_type: integer
        data_tests:
          - not_null
          - relationships:
              to: ref('person')
              field: person_id
      - name: visit_concept_id
        description: This field contains a concept id representing the kind of visit, likeinpatient or
          outpatient. All concepts in this field should be standardand belong to the Visit domain.
        data_type: integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: visit_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Visit'
      - name: visit_start_date
        description: For inpatient visits, the start date is typically the admission date.For outpatient
          visits the start date and end date will be the same.
        data_type: date
        data_tests:
          - not_null
      - name: visit_start_datetime
        description: ''
        data_type: datetime
      - name: visit_end_date
        description: For inpatient visits the end date is typically the discharge date. If aPerson is
          still an inpatient in the hospital at the time of the dataextract and does not have a visit_end_date,
          then set the visit_end_dateto the date of the data pull.
        data_type: date
        data_tests:
          - not_null
      - name: visit_end_datetime
        description: If a Person is still an inpatient in the hospital at the time of thedata extract
          and does not have a visit_end_datetime, then set thevisit_end_datetime to the datetime of the
          data pull.
        data_type: datetime
      - name: visit_type_concept_id
        description: Use this field to understand the provenance of the visit record, orwhere the record
          comes from.
        data_type: Integer
        data_tests:
          - not_null
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: visit_type_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Type Concept'
      - name: provider_id
        description: There will only be one provider per visit record and the ETL documentshould clearly
          state how they were chosen (attending, admitting, etc.).If there are multiple providers associated
          with a visit in the source,this can be reflected in the event tables (CONDITION_OCCURRENCE,PROCEDURE_OCCURRENCE,
          etc.) or in the VISIT_DETAIL table.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('provider')
              field: provider_id
      - name: care_site_id
        description: This field provides information about the Care Site where the Visit tookplace.
        data_type: integer
        data_tests:
          - relationships:
              to: ref('care_site')
              field: care_site_id
      - name: visit_source_value
        description: This field houses the verbatim value from the source data representingthe kind of
          visit that took place (inpatient, outpatient, emergency,etc.)
        data_type: varchar(50)
      - name: visit_source_concept_id
        description: ''
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: visit_source_concept_id not in (0, -99) # Emory Specific for "flavor" of null
      - name: admitted_from_concept_id
        description: Use this field to determine where the patient was admitted from. Thisconcept is part
          of the visit domain and can indicate if a patient wasadmitted to the hospital from a long-term
          care facility, for example.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: admitted_from_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Visit'
      - name: admitted_from_source_value
        description: ''
        data_type: varchar(50)
      - name: discharged_to_concept_id
        description: Use this field to determine where the patient was discharged to after avisit. This
          concept is part of the visit domain and can indicate if apatient was transferred to another
          hospital or sent to a long-term carefacility, for example. It is assumed that a person is discharged
          to hometherefore there is not a standard concept id for “home”. Use concept id= 0 when a person
          is discharged to home.
        data_type: integer
        data_tests:
          - dbt_utils.relationships_where:
              to: ref('concept')
              field: concept_id
              from_condition: discharged_to_concept_id not in (0, -99) # Emory Specific for "flavor" of null
              to_condition: domain_id = 'Visit'
      - name: discharged_to_source_value
        description: ''
        data_type: varchar(50)
      - name: preceding_visit_occurrence_id
        description: Use this field to find the visit that occurred for the person prior tothe given visit.
          There could be a few days or a few years in between.
        data_type: integer
        # tests:
        #   - relationships:
        #       to: ref('visit_occurrence')
        #       field: visit_occurrence_id
```