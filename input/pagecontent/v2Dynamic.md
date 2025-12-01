### Description

The V2-mediated matching approach can also facilitate dynamic Group creation by the Authorized User through the use of existing V2 query messaging. Upon receipt of a V2 QBP message, patient matching logic is executed, and the FHIR Patient resource ID is returned in the RSP message as an additional patient identifier. The Authorized User's system can store this Patient resource ID alongside the corresponding patient record. Subsequently, the Authorized User can utilize the methodology outlined in the Maintaining Cohorts section of this document to dynamically create and update Groups by adding or removing individuals as needed.

![V2-Mediated Matching (Dynamic Group Creation) Work Flow](v2Dynamic.png){:style="float: none; width:700px;margin-left:35px; display: block;"}
 
### Assumptions

The use of V2 messaging to return the FHIR Patient resource ID for Bulk Data Access is an additional functionality beyond the standard actions performed by the IIS when processing V2 messages, such as data quality analysis, reporting, and creating groups for other purposes.

Since the Authorized User system may already have an existing FHIR Patient resource ID associated with an individual, it will validate that the returned ID from the V2 query response is consistent with previous patient matching outcomes. This includes ensuring that the returned ID is not associated with a different individual.

To maintain accuracy in the FHIR resource ID known by the Authorized User system at the time of the Bulk Data query, trading partners will agree on the duration for which a high-confidence patient match remains valid before a new match must be conducted.

The Group ID will be shared with the Authorized User through out-of-band communication and will not be included in the V2 query response message (RSP). Alternatively, the Group ID could be treated as a non-unique patient ID in the RSP response message and stored with the patient in the Authorized User system to facilitate Group usage during the bulk query phase.

The decision to add or remove individuals from the Group will originate with the Authorized User system, although the IIS may reject a request to add an individual if it determines there is no reasonable relationship between the individual and the Authorized User.

### Group Types Supported

#### Transient Groups (enumerated)

The return of the FHIR Patient resource ID in the V2 query response enables the Authorized User system to dynamically create and update Groups, adding or removing patients as necessary to form transient Groups based on specific workflow requirements. For example, a transient Group of individuals with upcoming appointments can be created and queried.

#### Persistent Groups (enumerated)

The creation of Groups through existing V2 messaging allows for the formation of a singular Group of enumerated individuals associated with the Authorized Users. As new individuals are identified through V2 query messages, the Authorized User can incrementally add them to an existing Group, resulting in a persistent list of individuals selected by the Authorized User.

### Benefits

The V2-mediated approach reduces implementation efforts by leveraging the established V2 messaging framework. It utilizes existing patient matching logic, eliminating the need for separate matching logic solely for bulk data access. Many Authorized Users, particularly care-providing organizations, may already be exchanging V2 messages with the IIS, simplifying the onboarding process and testing. Once the Authorized User obtains an individual’s FHIR Patient resource ID, they have the flexibility to update one or more Groups, allowing for the creation of both transient and persistent Groups.

### Limitations

This approach is applicable only to organizations with a use case for V2 query messaging with an IIS, making it impractical for certain Authorized Users, such as schools or community-based organizations. Additionally, since the FHIR Patient resource ID must be returned, this approach is limited to V2-based query exchanges (QBP/RSP), as the standard acknowledgment for administration event messages (VXU) does not include a method for returning patient identifiers.

The stability of positive patient matches is uncertain. As patient demographics change, prior high-confidence matches may become outdated. This approach does not provide a mechanism for renewing patient matches without exchanging a new V2 QBP message.

### Technical Requirements

#### IIS HIT Vendor

In addition to supporting QBP/RSP V2 messaging, the IIS HIT vendor should implement the ability to return the FHIR resource ID for a high-confidence match in the RSP response to a QBP query message. The IIS HIT vendor must also support the ability for Authorized User system directed Group creation (optional) and the dynamic addition and removal of individuals to the Group.

#### Authorized User HIT Vendor

The Authorized User HIT vendor must support QBP/RSP V2 messaging with the IIS as well as the ability to store the FHIR Patient resource ID in association with the patient record upon receipt of the RSP response message. The Authorized User HIT vendor must also support the ability to support Group creation (optional) and the dynamic addition and removal of individuals to the Group.

### Operational Requirements

#### Immunization Program

The immunization program must support the implementation of real-time QBP/RSP V2 messaging with the Authorized User if not already live in Production. 

#### Authorized User

The Authorized User must support the implementation of real-time QBP/RSP V2 messaging with the immunization program if not already live in Production. The Authorized User must also develop and implement workflows and processes to update Groups with individuals as needed.

### Implementation Considerations

This approach should be implemented with a mutual understanding between trading partners that V2 QBP messages must only be triggered by the Authorized User system as part of standard clinical care activities. These messages should be used exclusively to obtain an individual's Patient FHIR resource ID. Utilizing V2 query messages for this purpose could place significant strain on the IIS query/response capacity.

When onboarding a new trading partner, the immunization program should determine whether the Authorized User is interested in performing Bulk FHIR queries. The V2-mediated approach does not need to be implemented for trading partners that do not plan to use Bulk FHIR queries. Additionally, the immunization program should check with existing trading partners regarding their interest in Bulk FHIR queries and implement this functionality as appropriate.
