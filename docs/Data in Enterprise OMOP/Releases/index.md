# Overview

Releases for Emory Enterprise OMOP are demarcated via semantic versioning 2.0, **x.x.x** (see [Semantic Versioning](https://semver.org/)). The versioning scheme is as follows:

- Major version: Incremented for major changes to the data model or ETL process.
    
    - **Note on versioning, Apr-23-2025**: We reserve major version increments for changes that have received considerable community feedback with > 40 users of our tools per month (initial starting point), with at least 5 daily active users. For the time being, widespread querying and adoption is more akin to targeted beta testing, so we seek to make improvements with our current users prior to rolling out and attempting to recruit at scale within Emory.

- Minor version: Incremented for minor changes to the data model or ETL process.

- Patch version: Incremented for bug fixes or minor changes that do not affect the data model or ETL process.

## Technology Used to Maintain Version Control

- **Github**: Enterprise and protected public version control and collaboration platforms utilized. See the development repo for comprehensive releases, including release notes and release information.
    - [ETL development repo](https://github.service.emory.edu/LITS/omop_emory_dbt/releases) holds the code that most users will interact with.
        - releases and all version information is housed here
    - [ETL production repo](https://github.com/EmoryDataSolutions/ds-dbt-projects) holds our production code
        - This is essentially a direct copy of the dev repo. 
        - Users will likely never have access to this repo as it is used purely by our administrator.

- **Jira**: Project management tool used to track issues and bugs. 
    - The Jira Enterprise OMOP Release Planning project is where we track all issues and bugs. If you report a but on this site, you are taken to a jira form. Please see the jira release notes for further details.