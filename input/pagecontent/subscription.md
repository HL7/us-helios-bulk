### Description

The [FHIR Subscription](https://www.hl7.org/fhir/subscription.html) paradigm provides an additional mechanism for creating Groups limited to individuals with known updates to their immunization data in the IIS. This approach relies on the IIS and Authorized User having shared the FHIR Patient resource ID, which can be accomplished through various methods described earlier in this document, including Bulk Patient Matching or V2 query/response.

Once the Authorized User possesses the Patient FHIR Resource ID, it subscribes to the individual via a Subscription Topic offered by the IIS. When the individual's record is updated in the IIS—potentially including changes to the Patient resource itself (such as demographic updates) or new, deleted, or updated immunization events—the IIS generates a notification bundle and sends it to the Authorized User.

The notification may consist of a comprehensive Bundle of resources, which could include Patient, Immunization, and ImmunizationRecommendation resources, allowing the Authorized User system to update without further data exchange. Alternatively, the notification may simply indicate which patient has been updated. The Authorized User can then add the updated Patient to a Group.

At a later time, the Authorized User initiates a Bulk Query, to which the IIS responds. Upon completion of the Bulk Query, the Group resource can be reset (removing all current patients) and reused as new notifications are received.

![Subscription to Patient with Bulk Work Flow](subscriptions.png){:style="float: none; width:1000px;margin-left:35px; display: block;"}
 
### Assumptions

The implementation of either a Patient Subscription Topic or an Immunization.patient Subscription Topic will trigger notifications for patient-level changes (e.g., demographic updates) as well as changes to immunization events (e.g., doses reported, updated, or deleted).

When using a Bulk Query to retrieve data, the _since parameter may still be necessary (unless a complete patient snapshot is desired) to obtain only recently added data. The value of _since will likely correspond to the date of the last bulk retrieval.

The Group ID will be shared with the user through out-of-band communication and will not be included in any responses sent back to the Authorized User system.

### Group Types Supported

#### Transient Groups (enumerated)

These Groups will be transient, meaning they will be reset periodically. This approach is designed to provide updates on stable patient populations rather than on individuals scheduled for upcoming visits.

### Benefits

The Authorized User receives proactive notifications, potentially in real-time, when a patient of interest is updated.

At any given time, the list of individuals in the Group will consist only of those who have been recently updated (as indicated by recent notifications). This should reduce the overhead associated with queries by limiting the enumerated list of Patients to those known to have updated data.

### Limitations

This approach does not address the underlying issue of positively identifying individuals across systems.

The introduction of FHIR subscription functionality requires support from both systems.

Authorized Users managing large cohorts of individuals may generate significant network traffic due to Subscription notifications.

### Technical Requirements

#### IIS HIT Vendor

The IIS HIT Vendor must support a mechanism for individual identification. Additionally, the vendor must implement the basic FHIR subscription paradigm, including support for Patient and/or Immunization.subject level Subscription Topics, and accept subscription requests from the Authorized User.

#### Authorized User HIT Vendor

The Authorized User HIT Vendor must support a mechanism for individual identification. The vendor must also enable the ability to subscribe to updates for specific individuals and to receive and process notifications. Furthermore, the Authorized User HIT Vendor must implement functionality to add individuals to a Group and reset a Group by removing all existing Patients after a query is made.

### Operational Requirements

#### Immunization Program

The immunization program must implement at least one mechanism for patient matching.

#### Authorized User

The Authorized User organization must implement at least one mechanism for patient matching as well as a workflow to trigger a Bulk Query (either scheduled or on demand).

### Implementation Considerations

Trading partners must agree on a mechanism for patient identification and the sharing of FHIR resource IDs, as well as the nature and robustness of the notifications sent.
