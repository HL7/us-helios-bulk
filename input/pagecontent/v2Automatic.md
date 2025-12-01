### Description

Many trading partners, especially healthcare organizations, have established V2 messaging with one or more immunization information systems (IIS). Two common use cases for this exchange include immunization administration event notifications (using the VXU message type) and patient history/forecast queries (using the QBP and RSP message types). For detailed implementation guidance on V2-based immunization exchange, refer to the [CDC HL7 v2.5.1 Implementation Guide for Immunization Messaging, Release 1.5](https://www.cdc.gov/iis/technical-guidance/hl7.html).

V2 messaging inherently includes processes for exchanging patient demographic data, which can be utilized to match existing patients in the IIS. These processes can serve as a precursor to FHIR-mediated Bulk Data access. The exchange of one or more V2 messages for a specific patient between an Authorized User and the IIS may indicate a relationship between the individual and the Authorized User. Upon receipt of the V2 message, patient matching logic is executed, allowing the IIS to add the patient to a FHIR Group resource for use by the Authorized User in a Bulk FHIR query.

![V2-Mediated Matching (Automatic Group Creation) Work Flow](v2Automatic.png){:style="float: none; width:700px;margin-left:35px; display: block;"}
 
### Assumptions

The use of V2 messaging to generate Groups for Bulk Data Access is an additional functionality beyond the standard actions performed by the IIS when processing V2 messages, such as data quality analysis, reporting, and creating groups for other purposes.

The FHIR Group resource, to which patients are added, is intended for use in Bulk FHIR queries representing the entire population of individuals associated with the Authorized User. Other groupings of individuals, whether using FHIR Group resources or alternative structures, may also exist for different purposes.

Typically, the Group ID will be shared with the Authorized User through out-of-band communication (e.g., during the onboarding process) and will not be included in any V2 response messages (ACK or RSP). Alternatively, the Group ID could be treated as a non-unique patient ID in an RSP response message and stored with the patient in the Authorized User's system to facilitate Group usage during the bulk query phase.

### Group Types Supported

#### Persistent Groups (enumerated)

The creation of Groups through existing V2 messaging allows for the formation of a singular Group of enumerated individuals associated with the Authorized User. This association may arise from individuals receiving a vaccine from the Authorized User (resulting in a VXU message) or being queried by the Authorized User (resulting in a QBP message), in accordance with local jurisdictional policies. As new individuals are identified through V2 messages, they can be incrementally added to the Group, creating a persistent list of individuals known to the Authorized User. Note that some jurisdictions may not establish a patient-care association based solely on the receipt of a QBP message and may therefore not choose to add the individual to the Authorized User’s Group based on this V2 exchange.

### Benefits

The V2-mediated approach minimizes implementation efforts for stakeholders by leveraging the established V2 messaging framework. Existing patient matching logic is utilized, eliminating the need for separate matching logic solely for bulk data access. Additionally, many IIS already establish relationships between individuals and Authorized Users based on V2 messaging. Consequently, many Authorized Users, particularly care-providing organizations, may already be exchanging V2 messages with the IIS, simplifying the onboarding process and testing.

### Limitations

The V2-mediated approach is applicable only to organizations with an independent use case for V2 messaging with an IIS, making it impractical for certain Authorized Users, such as schools or community-based organizations.

This approach does not provide a direct method for removing individuals from an existing Group; it can only add individuals as V2 messages are processed. An exception may occur if local policy allows only a single association, necessitating the removal of an individual from an existing Group before adding them to a new one. This limitation may hinder an Authorized User's ability to receive data on all their patients if individuals receive services from multiple providers.

Due to these constraints, the V2-mediated approach is not well-suited for creating transient Groups, where the composition changes significantly over time and there is a need to dynamically add or remove individuals based on triggers other than V2 messages.

### Technical Requirements

#### IIS HIT Vendor

In addition to supporting VXU and/or QBP/RSP V2 messaging, the IIS HIT vendor must implement functionality to assess the suitability of adding individuals identified during V2 message processing to a Group. Suitability criteria may include match certainty, the nature of the Authorized User, and local policies regarding the establishment of relationships between individuals and organizations.

#### Authorized User HIT Vendor

The Authorized User HIT vendor must support VXU and/or QBP/RSP V2 messaging with the IIS.

### Operational Requirements

#### Immunization Program

The immunization program must facilitate the implementation of real-time V2 messaging with the Authorized User if it is not already operational. Additionally, it must develop and implement rules for determining the suitability of adding identified patients to a Group for the Authorized User.

#### Authorized User

The Authorized User must support the implementation of real-time V2 messaging with the immunization program if it is not already operational.

### Implementation Considerations

When implementing V2 messaging for a new trading partner, the immunization program should inquire whether the Authorized User is interested in performing Bulk FHIR queries. The V2-mediated approach is not necessary for trading partners who do not intend to use Bulk FHIR queries. The immunization program should also check with existing trading partners regarding their interest in Bulk FHIR queries and implement this functionality as appropriate.

The criteria for adding individuals to specific Groups based on V2 message processing will be defined locally by the immunization program, including whether individuals may belong to multiple Groups simultaneously.
