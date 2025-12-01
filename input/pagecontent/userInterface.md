### Description

An IIS may provide an online portal that includes an interface for Authorized Users to search for individuals and add them to a Group for subsequent queries. Additional functionality should be implemented to enable users to manage existing Groups, such as removing individuals from a Group or resetting the Group to remove all members.


![Online User Interface Mediated Matching Work Flow](userInterface.png){:style="float: none; width:700px;margin-left:35px; display: block;"}
 
### Assumptions

Authorized Users will manually enter sufficient demographic information to positively identify an individual.

The Authorized User system will implement a mechanism that allows an end user to manually trigger a bulk query once a Group has been updated through the online interface.

The Group ID will be shared with the Authorized User through out-of-band communication.

### Group Types Supported

#### Transient Groups (enumerated)

When the size of a transient Group is manageable, an end user from the Authorized User may manually identify and add individuals to the Group. Given the nature of transient Groups, the need to remove an individual may be limited to correcting errors, but the ability to reset the Group to an empty state or create a new transient Group will likely be important.

#### Persistent Groups (enumerated)

Persistent Groups can be built over time by an end user from the Authorized User. The ability to dynamically add and remove individuals will be crucial for maintaining the Group over time.

### Benefits

An online interface allows end users to create and maintain cohorts of individuals without requiring additional patient matching or Group manipulation functionality in the Authorized User system. It also ensures that a human can resolve multiple possible matches or low confidence matches.

End users may already be familiar with IIS functionality for finding and accessing patient records, which will ease the transition to creating Groups for bulk queries.

### Limitations

For large cohorts, the effort required to identify individuals and manage Groups may be prohibitively high.

Manual entry of demographics by end users may lead to errors.

Using an online interface requires end users to leave the Authorized User system to identify individuals and create cohorts before returning to trigger the actual query.

### Technical Requirements

#### IIS HIT Vendor

The IIS HIT Vendor must support the ability for end users to search for patients and add them to a Group through an online interface.

#### Authorized User HIT Vendor

The Authorized User HIT Vendor must support the ability for an end user to manually trigger a bulk query.

### Operational Requirements

#### Immunization Program

The immunization program must implement an online portal for end user use.

#### Authorized User

The Authorized User must implement a workflow for end users to create Groups through the IIS online portal and then subsequently trigger the bulk query.

### Implementation Considerations

This approach may have limited applicability unless the expected Group size is small, or the Authorized User is unable to implement one of the other approaches.
