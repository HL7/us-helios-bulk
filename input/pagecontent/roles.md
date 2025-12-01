The "Who is Involved" section outlines four key roles in the bulk exchange of immunization data: the jurisdictional immunization program, the Health Information Technology (HIT) vendor for the immunization information system (IIS vendor), the Authorized User organization, and the Authorized User HIT vendor. The term "IIS HIT Vendor" also includes jurisdictions that develop and manage their own IIS locally. Each role must implement essential technical and operational functionalities to support bulk data queries, regardless of the specific technology or approach chosen by trading partners. These functionalities relate to topics in the Policy Considerations and Technical Concepts sections, detailed below.

### Foundational Technical Requirements

The Authorized User HIT Vendor should expect to develop functionality to:
- Support mechanisms for large-scale patient matching.
- Create and/or update Groups.
- Generate Bulk Data Access queries to immunization programs.
- Retrieve and process data in response to Bulk Data Access queries.
- Make retrieved data accessible to end users and automated processes to support organizational objectives.

The IIS HIT Vendor should expect to develop functionality to:
- Support mechanisms for large-scale patient matching.
- Create and/or update Groups.
- Receive and respond to Bulk Data Access queries from Authorized User organizations.

### Foundational Operational Requirements

The immunization program should expect to develop processes and functionality to work with the Authorized User organization to:
- Identify and document use cases for Group creation and Bulk Data query
- Create and communicate the Group IDs relevant to a given Authorized User organization
- Select appropriate approaches to patient matching, cohort creation and data exchange
    - This may include the need to identify and implement any rules or requirements that must be met in order for a patient to be added to a Group used by a particular Authorized User organization.
        - Criteria may include certainty of matching (i.e., what is a high-confidence match), establishment of a relationship between patient and organization and others
        - This evaluation may apply to a variety of methods for updating a Group including V2 mediated exchange (i.e., does a V2 message imply a relationship with an organization), flat-file mediated exchange (i.e., are all patients in the file appropriate to add to a Group), FHIR-operations (i.e., are all Patients OK to add or remove from a Group) and more
- Identify and execute any required agreements with the Authorized User organization  to exchange data on cohorts of individuals
- Implement IIS functionality required to support the agreed upon integration

The Authorized User organization should expect to develop processes and functionality to work with the immunization program to:
- Identify and document use cases for Group creation and Bulk Data query
- Select appropriate approaches to patient matching, cohort creation and data exchange
- Develop mechanisms to share Group IDs if the Authorized User organization is not permitted to directly create new Groups
- Identify and execute any required agreements with the immunization program to exchange data on cohorts of individuals
- Develop and implement processes and workflows necessary to:
    - Allow the selection of individuals for patient matching
    - Add identified individuals to a Group as appropriate
    - Remove individuals from a Group as appropriate
    - Deal with patient matching outcomes where a single high-confidence patient match is not found

