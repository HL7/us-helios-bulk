### Description

The authors of the Bulk Data Access FHIR IG are exploring a [method to identify groups of individuals across systems](https://build.fhir.org/ig/HL7/bulk-data/branches/bulk-match/match.html). They propose an asynchronous [$bulk-match operation](https://hl7.org/implement/standards/fhir/patient-operation-match.html), which is based on the standard $match operation. The response will include sets of matching Patient resources. The Authorized User system can store the Patient resource IDs alongside the corresponding patient records. Subsequently, the Authorized User can utilize the methodology outlined in the Maintaining Cohorts section of this document to dynamically create and update Groups by adding or removing individuals as needed.

![FHIR-Mediated Matching (Bulk) Work Flow](FHIRBulk.png){:style="float: none; width:700px;margin-left:35px; display: block;"}
 
### Assumptions

The outcome of patient matching in the IIS for any individual in the bulk matching request may yield zero, one, or multiple individuals in the response. Authorized User systems must be capable of handling all possible patient matching outcomes, including a single high-confidence match, multiple high-confidence matches, multiple low-confidence matches, a single low-confidence match, and no matches.

The Authorized User system is allowed to validate that the returned Patient resource for an individual matches the input data before associating the FHIR Patient resource ID with the patient record.

To ensure the accuracy of the FHIR resource ID known by the Authorized User system at the time of the Bulk Data query, trading partners will agree on the duration for which a high-confidence patient match remains valid before a new match must be conducted.

The Group ID will be shared with the user through out-of-band communication and will not be included in the query response. Alternatively, both systems must support the dynamic creation of Groups.

The decision to add or remove individuals from the Group will originate with the Authorized User system; however, the IIS retains the option to reject a request to add an individual if it determines there is no reasonable relationship between the individual and the Authorized User.

### Group Types Supported

#### Transient Groups (enumerated)

The return of the FHIR Patient resource ID in response to the $bulk-match operation enables the Authorized User system to dynamically create and update Groups, adding or removing patients as necessary to form transient Groups based on specific workflow requirements. For example, a transient Group of individuals with upcoming appointments can be created and queried.

#### Persistent Groups (enumerated)

Identifying individuals through the $bulk-match operation facilitates the creation of a singular Group of enumerated individuals associated with the Authorized Users. As new individuals are identified through a FHIR bulk match, they can be incrementally added to the Group, resulting in a persistent list of individuals known to the Authorized User.

### Benefits

The $bulk-match operation allows for the asynchronous matching of large numbers of individuals, minimizing the technical load on the IIS.

Authorized Users have the flexibility to independently update one or more Groups, enabling Group creation without imposing significant burdens on the immunization program.

### Limitations

The $bulk-match operation is still under development and may not be widely supported.

The stability of positive patient matches is uncertain. As patient demographics change, prior high-confidence matches may become outdated. This approach does not provide a mechanism to validate or renew matches without performing a new search for the individual.

### Technical Requirements

#### IIS HIT Vendor

The IIS HIT vendor must support the acceptance of data for a set of individuals to be matched, execute patient matching logic, and return any found matches in FHIR format. The vendor must also facilitate Authorized User system-directed Group creation (optional) and the dynamic addition and removal of individuals from the Group.

#### Authorized User HIT Vendor

The Authorized User HIT vendor must enable the system or an end user to identify a set of individuals for addition to or removal from a Group prior to a Bulk FHIR query. The Authorized User system must be capable of initiating bulk patient matching through the $bulk-match operation and validating and associating (if appropriate) a returned FHIR Patient resource ID with the individual’s record. The vendor must also support Group creation (optional) and the dynamic addition and removal of individuals from the Group.

### Operational Requirements

#### Immunization Program

Specific operational requirements for an immunization program have not yet been identified.

#### Authorized User

The Authorized User must support the implementation of workflows to allow an end user or the system itself to identify a set of individuals to be matched and initiate that process. The Authorized User must also develop and implement workflows and processes to update Groups with individuals as needed.

### Implementation Considerations

As Bulk FHIR Users are onboarded, the frequency of bulk patient matching requests should be discussed. Depending on the size of the set of individuals involved, the $bulk-match operation may be resource intensive, although the asynchronous nature of the operation may mitigate this concern somewhat.
