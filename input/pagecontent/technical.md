### Bulk Data Access FHIR IG Overview

The [FHIR Bulk Data Access Implementation Guide](https://hl7.org/fhir/uv/bulkdata/) (referred to as the Bulk Data IG) was developed and published by the [SMART Health IT project](https://smarthealthit.org/about-smart-2/) at Boston Children’s Hospital Computational Health Informatics Program. This standard addresses the need to efficiently access large volumes of information on groups of individuals. As of the time of publication of this document, the latest version of the implementation guide is version 2.0.0. All efforts undertaken by the Helios Bulk Data Priority Area have focused on this version of the standard. 

According to the [Bulk Data Export Operation Request Flow](https://hl7.org/fhir/uv/bulkdata/export.html#bulk-data-export-operation-request-flow) section of the Bulk Data IG, a bulk data exchange typically involves two primary roles: the Bulk Data Server and the Bulk Data Client. For a detailed description of the system interactions involved, refer to the Request Flow section mentioned above. In this document, we will refer to this sequence of interactions as the “bulk data query.” For the primary immunization use case discussed here, the Immunization Information System (IIS) implemented by the jurisdictional immunization program serves as the Bulk Data Provider (or Server), while the system used by the Authorized User acts as the Bulk Data Client. In many examples where the Authorized User is a healthcare provider, the system in use is expected to be an electronic health record (EHR), although other types of systems may be employed. We expect that the technical and workflow requirements will be substantially similar across all types of Authorized Users.

#### Operation Types

The Bulk Data IG defines three approaches to querying data through custom operations:
- [**FHIR Bulk Data System Level Export:**](https://hl7.org/fhir/uv/bulkdata/OperationDefinition-export.html) This operation exports data from a FHIR server, regardless of patient association.
- [**FHIR Bulk Data Group Level Export:**](https://hl7.org/fhir/uv/bulkdata/OperationDefinition-group-export.html) This operation retrieves a detailed set of FHIR resources of diverse types pertaining to all members of a specified Group.
- [**FHIR Bulk Data Patient Level Export:**](https://hl7.org/fhir/uv/bulkdata/OperationDefinition-patient-export.html) This operation obtains a detailed set of FHIR resources of diverse types pertaining to all patients.

For the immunization use case, the Bulk Data Priority Area primarily explored the Group Level Export operation, which forms the basis for much of this document. The System Level Export operation was too broad for this use case. Additionally, the concept of cohorts of individuals appropriate for sharing with a given Authorized User rendered the Patient Level Export operation similarly unsuitable. While the Patient Level Export does offer an experimental patient parameter that allows the client to specify a list of patient records to include in the response, Priority Area members felt that the potentially large size of the cohort could pose challenges for this operation. Therefore, focusing on the Group Level Export operation is more likely to yield productive results.

#### FHIR Group Resource

The FHIR [Group resource](https://www.hl7.org/fhir/group.html) represents a defined collection of entities that may be discussed or acted upon collectively. For the immunization use case, the Group resource serves as a mechanism to identify a cohort of individuals for a bulk data query. The Group resource can be utilized in two ways:
1.	**Enumerated List:** As a detailed list of members within the Group.
2.	**Descriptive Qualities:** As a description of the characteristics shared by Group members without explicitly listing each individual.

The Group Types section of this document further describes the possible applications for both methods of use. Note that the individuals listed within the Group are references to Patient resources stored on the same FHIR Server as the Group itself.

#### Parameters

Each of the three operations in the Bulk Data IG defines a set of input parameters that specify the output of the query response. Below are a few important input parameters for the Group Level Export operation, based on our exploration for pilot and production implementations. Readers should consult the Bulk Data IG for a complete description of all possible parameters for each of the three export operations.

- **_since**
    - **Server Usage:** Required
    - **Client Usage:** Optional
    - **Description:** Resources will be included in the response if their state has changed after the supplied time.
- **_type**
    - **Server Usage:** Optional
    - **Client Usage:** Optional
    - **Description:** A string of comma-delimited FHIR resource types. The response SHALL be filtered to only include resources of the specified resource types(s).
- **patient**
    - **Server Usage:** Optional
    - **Client Usage:** Optional
    - **Description:** A list of Patient resource references. Data will not be returned for Patients who are not included in this list.

The _since parameter may be important for retrieving data on individuals over time. When querying a cohort multiple times over an extended period, including the _since parameter allows the exchange of only the data that has changed since the last query. By setting this parameter to the date and time of the last query, the system can significantly reduce the volume of data transferred, limiting it to just the changes since the previous query. This reduces the burden on both the server supplying the data and the client processing it.

The _type parameter ensures that Authorized Users receive only the data types they are interested in and eligible to access. In the immunization use case, this may be less critical due to the focused nature of the IIS (see the Data Payload section for details on the resources likely to be exchanged), but it may play a larger role in other public health scenarios where the data source encompasses a broader range of information.

The use of the patient parameter is expected to be limited to specific use cases where the cohort size is relatively small, making it feasible to include an enumerated list of individuals in the bulk data query.

#### Security Expectations

Any interoperability implementation must carefully consider the security and privacy implications of data sharing, and the bulk exchange of public health information is no exception. While this document does not explicitly outline security expectations for FHIR bulk data exchange, it strongly encourages implementers to establish appropriate access controls to ensure adequate data protection. For immunization-specific use cases, providers may model their FHIR access controls on existing controls used for HL7 V2 messaging, influenced by local regulations and policies.

The FHIR base standard explicitly states that it is not a security protocol. Instead, it emphasizes that FHIR-based exchange protocols must be used in conjunction with various security protocols documented outside the FHIR base standard. The Bulk Data IG recommends that providers implementing bulk data utilize OAuth 2.0 access management in accordance with the [SMART Backend Services Authorization Profile](http://www.hl7.org/fhir/smart-app-launch/backend-services.html). For further information on security expectations, please refer to the [FHIR Security](https://hl7.org/implement/standards/fhir/security.html) section of the base standard.

### Technology Implementation

Support for the FHIR standard can be implemented in various ways. Implementers of a bulk data solution for public health use cases should carefully consider their chosen technical architecture. This document aims to provide system architecture guidance for those responding to bulk data queries for immunization history data, such as state immunization programs.

Given the considerable differences in program environments, existing infrastructure, usage volume, and other factors, the Helios effort does not recommend a single "best" architecture. Instead, it shares architectural options and considerations for applying them to specific environments and needs. Implementers developing a solution should assess their organization's existing infrastructure and collaborate with other IT initiatives to implement FHIR locally.

This guidance was developed by a Helios project team tasked with defining how an Immunization Information System (IIS) may need to set up an architecture to support Bulk FHIR queries. The considerations below are informed by the experiences of immunization organizations currently pursuing FHIR capabilities.

#### FHIR Implementation

Two common approaches are implementation as either a FHIR server or a FHIR façade. Thorough discussions of both of these approaches are documented online however a brief overview is provided below. 

A FHIR server is a processing environment (with associated data stores) designed specifically to maintain and produce FHIR resources. In contrast, a FHIR façade is an intermediary that can interact with external querying systems using FHIR. It accepts FHIR bulk queries and returns FHIR resources while interfacing with internal systems and data stores that are not FHIR-aware, compiling requested information and converting it to the FHIR format.

The choice between using a FHIR server or a FHIR façade may depend on the nature of the query data store:
- If the data is maintained in the IIS’s primary, non-FHIR-oriented format, a FHIR façade is necessary to bridge the gap between FHIR and internal formats.
- If the data is in a form that natively supports FHIR representation, either as actual FHIR resources or data mapped to a FHIR model, a FHIR server may be more appropriate.

Implementation considerations related to FHIR servers and façades include:
- FHIR Server
    - A FHIR server can natively support bulk queries.
    - There is no performance impact on the production IIS system.
    - Data transformation must occur as data is copied to the FHIR server. This transfer can be real-time or periodic.
    - Implementing a FHIR server may require additional development to support other functionalities (e.g., evaluation and forecasting) using FHIR-formatted data.
    - There are costs associated with maintaining a copy of the data in FHIR format.
- FHIR Façade
    - Minimal transformation or processing is required outside of the façade, meaning not all data may need to be copied.
    - There may be no performance impact on the production IIS system, depending on how data is staged.
    - The FHIR façade must be developed and supported.
    - There may be no costs associated with maintaining a replica of the data, depending on how data is staged.
    - Some IIS functionalities (e.g., patient matching, evaluation/forecasting) may still need to be replicated.

#### Staging of Data

The concept of data staging is closely related to the choice between using a server or a façade, particularly regarding the source of the data used to respond to incoming queries. The immunization data that serves as the source for responses can be managed in various ways. A fundamental decision is whether to use the primary Immunization Information System (IIS) data store or a replica of that data. In other words, will the IIS’s transactional data store be queried directly, or will the query access a copy of the data? If a replica approach is implemented, the frequency of data replication must also be considered: will the replica be updated continuously as the transactional data store is updated (as a parallel mirror), or will it be copied periodically?

Considerations when selecting the method for staging data may include:
- The size of the jurisdiction’s data set
- The expected frequency of data queries
- The size of data expected to be returned in response to a given query
- The importance of timeliness of data
- The performance impact on other IIS functions

Implementation considerations include:
- Direct Access
    - No need to maintain replica data, resulting in no additional costs.
    - The data is always up to date.
    - There is no need to replicate core functionality.
    - The FHIR façade must be developed and supported.
    - Possible performance impact on the production IIS system, depending on the frequency and size of queries.
- Periodic Copy 
    - No performance impact on the production IIS system.
    - Mirrored data may be used for other analytical purposes.
    - Depending on the refresh period, the data may not be fully up to date.
    - Scheduling of updates may be affected by other data uses (e.g., availability for a Data Mart).
    - There are costs associated with maintaining a mirror of the data.
    - Some IIS functionalities (e.g., patient matching, evaluation/forecasting) may need to be replicated.
- Parallel Mirror
    - The data is always up to date.
    - No performance impact on the production IIS system.
    - Mirrored data may be used for other analytical purposes.
    - The FHIR façade must be developed and supported.
    - Some IIS functionalities (e.g., message processing, deduplication, evaluation/forecasting) may need to be replicated.
    - There are costs associated with maintaining a mirror of the data.

#### General Considerations 

The following section illustrates the relationship between FHIR implementation and data staging concepts. Understanding how these elements interact is essential for developing an effective bulk data exchange strategy. Properly aligning the implementation approach with the chosen data staging method can enhance system performance, ensure data accuracy, and optimize resource utilization.

![Data staging - FHIR Server vs Facade](dataStaging.png){:style="float: none; width:1000px;margin-left:35px; display: block;"}

### Group Types

A Group that is the subject of a Bulk Data query may fall into a few categories, depending on how the group is defined and the rate of change among its members. Generally, a group can consist of either an enumerated list of specific individuals for a population of interest or a description of the characteristics of the individuals that make up the population without explicitly listing them.

In the case of enumerated lists, individuals can be added to or removed from the Group as needed, ensuring that the list of relevant individuals is defined at any given time. Conversely, a characteristic-based Group would likely expand to a list of specific individuals only at the time of responding to the query. This "just-in-time" determination may impact query response time, as the responding system must first identify the appropriate individuals that meet the Group's characteristics before retrieving and making the data available. The optimal type of Group will depend heavily on the query's use case and the needs of the trading partners.

#### Persistent Groups (enumerated)

A persistent Group consists of a stable list of individuals, where the majority of members remain associated with the Group over time. While new individuals may be added or existing individuals removed, this type of Group is expected to be queried multiple times over an extended period. Example use cases include:

- A healthcare organization (HCO) creating a list of patients under long-term care.
- A payer maintaining a list of members for whom they are responsible.
- A research program tracking a defined list of individuals over time.

In the "Mechanisms for Patient Identification and Group Creation" section below, several approaches to creating and maintaining persistent Groups are described. While these approaches may allow for the removal of individuals, implementers should regularly review and maintain persistent Groups to ensure that the individuals included remain active and relevant. Without regular reviews, Groups may become bloated over time, negatively impacting system performance and efficiency by inflating data exchanges with unnecessary information.

#### Transient Groups (enumerated)

A transient Group consists of a list of individuals expected to change frequently or to be queried only once. In most cases, the Authorized User is the sole source of information about the Group's content, and the IIS may not have a way to identify individuals to add or remove independently. Example use cases include:

- An HCO creating a list of patients scheduled for appointments the following day, updating the list daily with new individuals.
- A school district generating a list of incoming kindergarten students and executing a bulk query before the start of the school year.

Use cases involving relatively small enumerated transient Groups may also benefit from the [Patient Operation](https://hl7.org/fhir/uv/bulkdata/OperationDefinition-patient-export.html), where the experimental patient input parameter specifies a list of Patient resources to include in the response as an alternative to using a Group.

#### Characteristic Groups

A characteristic Group consists of a set of qualities that describe the members of the Group. Individuals may be included or excluded based on these characteristics, with membership determined dynamically during the response to a query. For example, an Authorized User might create a Group that includes all individuals under a specified age with recorded immunizations residing in a specific zip code, allowing for data exploration without needing to create an enumerated list in advance.

While characteristic-based Groups may be useful for certain scenarios, this document will primarily focus on the use of enumerated Groups for the remainder of the discussion.

### Maintaining Cohorts

Once a Group has been established on the FHIR server for a particular use case serving an Authorized User, the next step is to establish an initial enumerated list of individuals belonging to the Group and to maintain its membership over time. While both Persistent and Transient Groups must be initially established, the maintenance of a Group is more relevant to Persistent Groups. As described in the Mechanisms for [Patient Identification and Group Creation](identification.html) section of this document, there are multiple possible approaches to populating and maintaining a list of individuals in a Group. This section outlines general considerations for maintaining a cohort of individuals.

#### Considerations for maintaining a cohort
- **Group Size:** For use cases involving a persistent Group associated with a healthcare organization or payer, the number of individuals may be substantial. This can impact both the maintenance of the Group and the response times when querying the Group.
- **Patient Resource References:** The Group.member.entity field should reference a Patient resource contained on the IIS FHIR server, not a Patient resource defined on a different system (e.g., it should not reference a Patient resource on the FHIR client).
- **Data Persistence:** Depending on the technical architecture of the FHIR server implementation, the list of individuals in Group.member.entity may be stored in a manner consistent with the underlying IIS (e.g., in a relational database table or another mechanism).
- **Updates from Patient Resource Activities:** Activities affecting Patient resources in the IIS, which are independent of the Group's maintenance, may still require updates to the Group.
   - For example, if two patient records are merged in the data source and both original records are in the Group, the non-surviving record should be removed from the Group. If a new record is created for the merged individual, this new record should be added to the Group.
   - Conversely, if a single patient record incorrectly consolidates data from two separate individuals, the record may need to be "unmerged," resulting in two new patient records. This may necessitate removing the original record from the Group and adding one or both of the new records as appropriate.
- **Categorization Changes:** As individuals are categorized in the IIS, such as maintaining associations with external organizations like providers or schools, Group memberships may need to be updated.
- **Responsibility for Maintenance:** Depending on the use case served by the Group, either the Authorized User system or the IIS may need the capacity to add or remove patients from the Group.
   - In some cases, the IIS may take on this responsibility, particularly when it uses its own grouping logic to create persistent Groups for a specific Authorized User organization.
   - Alternatively, the Authorized User organization may be responsible, especially when using transient Groups where it is the only system that knows the Group's membership, such as when a healthcare organization queries for individuals with upcoming appointments or a school district queries for all students in a particular grade.
   - When onboarding an Authorized User, both trading partners must agree on which organization will bear primary responsibility for maintaining the Group.
- **New Operations in FHIR Release 5:** The FHIR Bulk Data Access Implementation Guide is based on [FHIR Release 4](http://hl7.org/fhir/R4). Beginning with [FHIR Release 5](http://hl7.org/fhir/R5), new [operations for managing large resources](https://hl7.org/implement/standards/fhir/operations-for-large-resources.html) are defined, including operations to add items to a Group (the [$add operation](https://hl7.org/implement/standards/fhir/resource-operation-add.html)) and remove items from a Group (the [$remove operation](https://hl7.org/implement/standards/fhir/resource-operation-remove.html)). These operations are similar to those developed by the [Da Vinci FHIR Accelerator](https://www.hl7.org/about/davinci/index.cfm) as part of their [Member Attribution (ATR) List Implementation Guide](https://hl7.org/fhir/us/davinci-atr/). While neither the R5 nor ATR operations may be directly supported by FHIR-based systems interested in Bulk FHIR, they provide valuable insights for implementing operations to maintain cohorts of individuals in a Group resource.

### Data Payload

The data payload returned in response to a Bulk Data query can vary. While this document does not specify the exact contents of a bulk query response for the immunization use case, it outlines the resource types that may be included in the response payload. Readers should refer to the Parameters section above for an introduction to the _type parameter, which specifies the response payload.

For queries to an Immunization Information System (IIS) for immunization-related data, expect at least two resource types: Patient and Immunization. These resources together describe the individual and a list of immunization events recorded by the IIS. Responses containing FHIR Patient and Immunization resources represent the immunization history for the set of individuals and may also include related resource types such as Practitioner, Location, Organization, RelatedPerson, ImmunizationRecommendation, and ImmunizationEvaluation to provide a comprehensive view of the patient’s history and context.

To align with existing implementation and regulatory requirements, it is highly recommended to adhere to US Core profiles when responding with data. The specific version of US Core selected may depend on trading partner needs and other FHIR implementations within the public health jurisdiction, but it is advisable to choose a version that complies with health IT regulations.

![Immunization Data Payload](payload.png){:style="float: none; width:1000px;margin-left:35px; display: block;"}

Data exchange partners must agree on the query response payload during the onboarding process to ensure that all data exchanges are appropriate for the involved parties.