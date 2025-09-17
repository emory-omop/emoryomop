# 🔌 Device Exposure

The `device_exposure` table captures **medical devices used on or implanted in a patient**, such as catheters, pacemakers, orthopedic implants, glucose monitors, or stents. These entries represent either **documented use, insertion, or supply of a device**, typically recorded in surgical records, supply chain systems, or EHR documentation modules.

In Epic or Cerner, this information may come from **procedural documentation, implant registries, anesthesia records**, or even **supply tracking systems** in the OR. Some data pipelines also derive it from **claims for durable medical equipment (DME)**.

Each row represents a **single device exposure event**.

## Key OMOP Fields Mapped to Familiar EHR Concepts

| OMOP Field | EHR Analogy | Description | Clinical Relevance |
|------------|-------------|-------------|---------------------|
| `device_exposure_id` | Device record ID | Unique identifier for the device exposure event. | Used internally for joins. |
| `person_id` | Patient ID | Foreign key to `person`. | Ties the device record to the patient. |
| `device_concept_id` | Device catalog / SNOMED concept | Standard concept representing the device used (e.g., "Total hip prosthesis", "Central venous catheter"). | Use for cohort definitions or exposure tracking. |
| `device_exposure_start_date` | Implant or insertion date | Start date of device usage (e.g., implant date). Time in `device_exposure_start_datetime`. | Critical for temporal alignment with procedures or outcomes. |
| `device_exposure_end_date` | Removal or usage end | If known, the end of exposure (e.g., removal of catheter). Often null. | Used for defining duration of exposure or device-related risk. |
| `device_type_concept_id` | Source/context type | Indicates origin of data (e.g., EHR, registry, billing). | Helpful for understanding context and reliability. |
| `unique_device_id` | UDI / barcode | Device identifier from supply chain or implant logs. Not always populated. | Useful for FDA surveillance or registries. |
| `quantity` | Count of devices used | Number of devices (e.g., 2 stents in one procedure). | Important in cardiovascular and orthopedic analyses. |
| `provider_id` | Performing provider | Who inserted or documented the device. | Supports attribution analyses or team-based reviews. |
| `visit_occurrence_id` | Related visit | Visit when the device was used. | Anchors device use to an encounter setting. |
| `device_source_value` | Local device code | Original EHR/supply system code for the device. | Useful for mapping QA or inventory tracking. |
| `device_source_concept_id` | Source code’s mapped concept | Concept ID mapped from the raw code. | May help trace source-device relationships. |

## Common Pitfalls and What to Watch For

- **Often sparsely populated**: Many EHR pipelines don’t yet load robust device data, and this is true for Emory (see Known Quality Issues to monitor for updates).
- **Device end dates often null**: Implants may not have removal dates unless explanted or expired.
- **Device naming can be vague**: Concepts are still maturing—validate `device_concept_id` definitions when building cohorts.

## Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| How many patients have received orthopedic implants? | `device_concept_id` filtered for joint prostheses |
| What is the average time between pacemaker insertion and first complication? | `device_exposure` (pacemaker) + `condition_occurrence` (complication) |
| Are urinary catheters associated with increased infection rates? | `device_exposure` (catheter) + `condition_occurrence` (UTI) |
| How many patients had central lines placed in the ICU? | `device_exposure` + `visit_detail` (filtered for ICU) |
| What types of devices are most frequently removed within 30 days? | `device_exposure_end_date` − `device_exposure_start_date` |