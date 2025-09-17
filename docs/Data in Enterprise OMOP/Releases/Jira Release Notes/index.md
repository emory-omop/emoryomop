# Overview

This page tracks most of the tasks completed for the release via project management software used by the Enterprise OMOP team. The tasks are organized by the release they are associated with. The tasks are also organized by the type of task, such as bug fix, new feature, or documentation update. Members of the Emory Community should be able to access all links included below. If you are unable to gain access, please contact us.

## Notes on Using Jira

- The Jira [Enterprise OMOP Release Planning](https://emoryrhsit.atlassian.net/jira/software/projects/OMOPRP/boards/947/backlog?epics=visible&versions=visible) project is where we track all issues and bugs. If you report a bug on this site, you are taken to a jira form.
- Every jira issue is associated with:
    - Enterprise OMOP Release Version (semantic versioning) (see the releases page for more information on our github repo)
        - These are the official community releases
    - Issues are organized by epics (purple), which are groupings of related issues.
        - **Quality Assurance** (QA) is the epic for issues related to testing and validation of the ETL process internally.
        - **Quality Control** (QC) is the epic for issues related to testing and validation of issues reported by users.
        - **Documentation** is the epic for issues related to documentation and user guides.
        - **Feature** is the epic for issues related to new features or enhancements Enterprise OMOP broadly.
    - Data Sample Version (by date of full or partial ETL pipeline run)
        - These are the versions of the datasets that were loaded at the time the task was created.
        - This feature was implemented as jira issues are typically only applicable to a specific version of the dataset, which itself is associated with a specific version of the ETL process.
            - Should an audit ever need to be performed, dataset version can be used to identify the commit dates of the code in the repo to understand code state at the time the Jira issue was created.
    - Patient Subsample Version (by date that the patient subsample was created)
        - These are small typically randomly selected samples of the dataset that are used for unit testing of changes to code en route to feature development or bug fixes.
        - This is usually only filled in if QA is being performed.