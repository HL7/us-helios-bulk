### Description

An immunization program may have already implemented functionality within their IIS to create associations between individuals and organizations for purposes other than performing a Bulk FHIR query. For instance, an IIS might associate individuals with a provider or healthcare organization for quality reporting. These associations can be replicated within a FHIR Group, which can then be referenced in a Bulk FHIR query.

It is important to note that this approach does not require the IIS to alter the nature of the associations they are tracking or to use the existing sets of individuals for Bulk FHIR queries. Instead, it simply involves replicating the sets of individuals in FHIR Group format to make them accessible for bulk querying.

![Reuse of Existing Groups Work Flow](existing.png){:style="float: none; width:700px;margin-left:35px; display: block;"}
 
### Assumptions

The requirements for creating the original associations should significantly overlap with the needs of the Bulk FHIR query.

The IIS may provide the capability to seed a Group based on existing associations while allowing the User to modify the Group through other mechanisms.

As associations are updated through these mechanisms, the IIS will actively maintain the Group as well.

The Group ID will be shared with the Authorized User through out-of-band communication.

### Group Types Supported

#### Persistent Groups (enumerated)

Existing functionality to maintain cohorts of individuals in an IIS for functionality unrelated to bulk query is mostly likely to align best with persistent Groups that are maintained over time. 

### Benefits

Some IIS already maintain groups of individuals associated with external partners that may be suitable for use. Therefore, the logic necessary to establish these relationships should already exist, requiring minimal new development beyond replicating the associations in the form of a FHIR Group.

### Limitations

Grouping logic may be implemented differently across various IIS, introducing variability for HIT vendors or Authorized Users operating across jurisdictions.

Existing grouping logic may not align well with the preferences of Authorized Users for performing queries. Specifically, the existing logic may not provide Authorized Users access to all individuals for whom they are responsible.

### Technical Requirements

#### IIS HIT Vendor

The IIS HIT Vendor must support leveraging existing grouping functions to add individuals to a FHIR Group.

#### Authorized User HIT Vendor

Specific operational requirements for an Authorized User HIT vendor have not yet been identified.

### Operational Requirements

#### Immunization Program

The immunization program must implement functionality to establish cohorts of individuals suitable for association with an Authorized User. Additionally, the immunization program must establish an understanding with the Authorized User to reuse existing grouping functionality for bulk query purposes.

#### Authorized User

The Authorized User must establish an understanding with an immunization program to reuse existing grouping functionality for bulk query purposes.

### Implementation Considerations

When an Authorized User expresses interest in Bulk FHIR queries, existing associations should be reviewed to determine if they can serve as the basis for Group creation and maintenance.
