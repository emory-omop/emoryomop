# 📍 Location

The `location` table contains **geographic and address-level data** for people, care sites, and providers. It represents physical locations—such as **home addresses, facility addresses, or clinic sites**—but does *not* include specific hospital rooms. GPS coordinates including latitude and longitude may be included if available.

In Epic or Cerner, this corresponds to **patient registration addresses**, **facility address records**, or **provider contact locations**. In OMOP, the `location_id` is referenced by other tables (like `person`, `care_site`, and `provider`) to indicate where something or someone is located.

### Key OMOP Fields Mapped to Familiar EHR Concepts

| OMOP Field | EHR Analogy | Description | Clinical Relevance |
|------------|-------------|-------------|---------------------|
| `location_id` | Address ID | Unique identifier for the location. | Used to link addresses to patients, providers, or sites. |
| `address_1`, `address_2` | Street address | Line 1 and optional line 2 of the street address. | Typically suppressed or redacted in de-identified datasets. |
| `city` | City | City name. | Useful for geographic stratification. |
| `state` | State | State or province code (e.g., "GA"). | Enables regional cohort definitions. |
| `zip` | ZIP or postal code | 5- or 9-digit code. | Used for zip-code level analyses or geocoding. |
| `county` | County name | May be derived from zip. | Helpful for public health mapping or rural-urban classifications. |
| `location_source_value` | Original address ID | Local address ID from the source system. | Useful for crosswalks and QA. |
| `country_concept_id` | Country concept | OMOP concept representing the country (e.g., United States). | Useful for international datasets or cross-border studies. |
| `latitude`, `longitude` | Geo-coordinates | Not always populated; supports geospatial analysis. | Critical in studies involving environmental exposures or neighborhood-level SDoH. |

### Common Pitfalls and What to Watch For

- **De-identification**: Address fields are often partially or fully removed in HIPAA-limited datasets. Only select staff at Emory will have full access to this table.
- **ZIP codes may not be enough**: Many analyses require linking zip to census tract or SDoH indices—this must be done outside OMOP or via extensions.
- **Location ≠ care site**: The `location` table is used *by* `care_site` and `provider`, not a substitute for those.

### Clinical Use Cases

| Question | Where to Look |
|----------|----------------|
| How many patients reside in rural zip codes? | `person.location_id` + `location.zip` + rural-urban classification crosswalk |
| What are the regional variations in hypertension treatment? | `drug_exposure` + `person.location_id` + `location.state` |
| Which care sites are located in medically underserved areas? | `care_site.location_id` + `location.zip` |
| Can we stratify outcomes by patient neighborhood-level deprivation index? | `location.zip` + external mapping to ADI or SVI |
| What proportion of patients reside within 10 miles of our primary cancer center? | `person.location_id` + `location.latitude/longitude` + geodistance calculation |