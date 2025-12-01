### Description

The FHIR base standard provides several methods for identifying an individual across systems. This includes searching for a [Patient resource](https://hl7.org/implement/standards/fhir/patient.html) and utilizing the [$match operation](https://hl7.org/implement/standards/fhir/patient-operation-match.html). Both methods incorporate the individual's demographics as known by the Authorized User system during the search process and can return zero, one, or multiple Patient resource IDs in the response. The Authorized User system can store the Patient resource ID in association with the corresponding patient record. Subsequently, the Authorized User can apply the methodology outlined in the  Maintaining Cohorts section of this document to dynamically create and/or update Groups by adding and/or removing individuals as necessary.

![FHIR-Mediated Matching (Individual) Work Flow](FHIRIndividual.png){:style="float: none; width:700px;margin-left:35px; display: block;"}
 
### Assumptions

The outcome of patient matching in the IIS may result in 0, 1 or multiple individuals being returned as part of the response. Authorized User systems are expected to be able to handle all possible patient matching outcomes including a single high-confidence match, multiple high-confidence matches, multiple low-confidence matches, single low-confidence match and no matches.

The Authorized User system is permitted to validate the returned Patient resource matches the input data before associating the FHIR Patient resource ID with the patient record in their system.

In order to ensure that the FHIR resource ID known by the Authorized User system is accurate at the time of the Bulk Data query, trading partners will agree upon how long a high-confidence patient match is valid for before a new patient match must be conducted.

The Group ID will be shared with the Authorized User through out-of-band communication and will not be included in the query response. Alternatively, both systems must support the dynamic creation of Groups.

The decision to add or remove individuals from the Group will originate with the Authorized User system; however, the IIS retains the option to reject a request to add an individual if it determines that there is no reasonable relationship between the individual and the Authorized User.

### Group Types Supported

#### Transient Groups (enumerated)

The return of the FHIR Patient resource ID in response to a Patient search or $match operation enables the Authorized User system to dynamically create and update Groups, adding or removing patients as necessary to form transient Groups based on specific workflow requirements. For example, a transient Group of individuals with upcoming appointments can be created and queried.

#### Persistent Groups (enumerated)

Identifying individuals through a Patient search or $match operation facilitates the creation of a singular Group of enumerated individuals associated with the Authorized Users. As new individuals are identified through these operations, they can be incrementally added to the Group, resulting in a persistent list of individuals known to the Authorized User.

### Benefits

Most FHIR-enabled systems are likely familiar with the Patient search and possibly the $match operation, offering existing functionality that can be readily implemented.

Authorized Users have the flexibility to independently update one or more Groups, allowing for Group creation without imposing significant burdens on the immunization program.

### Limitations

When dealing with large Groups, matching individuals one-by-one may require numerous interactions, potentially straining both systems.

The stability of positive patient matches is uncertain. As patient demographics change, prior high-confidence matches may become outdated. This approach does not provide a mechanism to validate or renew matches without performing a new search for the individual.

### Technical Requirements

#### IIS HIT Vendor

The IIS HIT vendor must support the acceptance of data for individual matching, execute patient matching logic, and return any found matches in FHIR format. The vendor must also facilitate Authorized User system-directed Group creation (optional) and the dynamic addition and removal of individuals from the Group.

#### Authorized User HIT Vendor

The Authorized User HIT vendor must enable the system or an end user to identify individuals for addition to or removal from a Group prior to a Bulk FHIR query. The Authorized User system must be capable of initiating patient matching through either a Patient search or the $match operation, as well as validating and associating (if appropriate) a returned FHIR Patient resource ID with the individual’s record. The vendor must also support Group creation (optional) and the dynamic addition and removal of individuals from the Group.

### Operational Requirements

#### Immunization Program

Specific operational requirements for an immunization program have not yet been identified.

#### Authorized User

The Authorized User must implement workflows that allow an end user or the system to identify individuals for matching and initiate that process. Additionally, the Authorized User must develop and implement workflows and processes to update Groups with individuals as needed.

### Implementation Considerations

As Bulk FHIR Users are onboarded, it is essential to discuss the system impact of potentially conducting a significant number of individual queries. The nature and size of the Groups expected to be utilized will be critical factors in determining the feasibility of this approach for a given implementation.
