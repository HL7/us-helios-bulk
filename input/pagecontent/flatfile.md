### Description

Currently, the bulk exchange of immunization data frequently relies on flat file exchanges. A system generates a list of individuals, including the demographic data necessary for patient matching, and shares this file with the immunization program via SFTP or another method. The immunization program then uses the flat file to identify matching individuals and collect their immunization history data. This data is subsequently formatted into a flat file and returned to the Authorized User.

There are no established standards for generating either the input or output files, leading to custom file formats that create unnecessary work for both trading partners. However, since flat file processes are already supported by existing systems, using flat files to generate Groups for FHIR-based bulk data queries may serve as an appealing entry point for trading partners.

Purpose-developed flat files produced by the Authorized User system can be utilized to share patient demographics for multiple individuals. The IIS can ingest the file and execute patient matching, adding matched individuals to a Group resource for later querying. The criteria for adding a patient to a specific Group will be defined locally by the IIS in consultation with the Authorized User.


![Flat File Mediated Matching Work Flow](flatFile.png){:style="float: none; width:700px;margin-left:35px; display: block;"}
 
### Assumptions

The Authorized User system can generate flat files according to agreed-upon specifications specifically for creating a FHIR Group for Bulk FHIR queries.

The FHIR Group resource is intended solely for use in Bulk FHIR queries. Other groupings of individuals, whether using FHIR Group resources or alternative structures, may also exist.

The Group ID will be shared with the Authorized User through out-of-band communication.

### Group Types Supported

#### Transient Groups (enumerated)

The contents of the input flat file are at the discretion of the Authorized User; however, this flat file-mediated approach is best suited for transient groups of individuals. For example, a transient Group of individuals with upcoming appointments can be created and queried. The number of individuals included in the flat file and the frequency of submissions may vary.

#### Persistent Groups (enumerated)

Generating persistent Groups is possible, but trading partners must agree on the mechanisms for adding and removing individuals from the Group using flat files. When a new flat file is considered an addendum to the existing Group, new individuals submitted can be incrementally added to create a persistent list of individuals known to the Authorized User. However, removing existing individuals from the Group may be more challenging with the flat file approach.

### Benefits

Trading partners with existing workflows that utilize flat files will be familiar with this process. For systems that currently support flat file exchanges, this approach minimizes the development effort required.

Authorized Users have the flexibility to generate flat files as needed, facilitating the creation of transient Groups.

### Limitations

The exchange of a flat file is not supported by a standard FHIR workflow and must be negotiated locally.

Unless a standardized flat file format is agreed upon, IIS will have to adapt to the make up of the flat file provided by Authorized Users (or vice versa).

The use of flat files for patient matching represents only an incremental improvement on the current flat file mediated processes.

### Technical Requirements

#### IIS HIT Vendor

The IIS HIT vendor must support the ability to receive and process a flat file containing demographics for multiple individuals and execute patient matching logic. They must also facilitate the addition of appropriate matches to a Group.

#### Authorized User HIT Vendor

The Authorized User HIT vendor must enable end users to generate a list of individuals for inclusion in a Group and create and transmit a flat file containing the relevant demographics in accordance with agreed-upon requirements.

### Operational Requirements

#### Immunization Program

The immunization program must establish an agreed-upon process and format with the Authorized User for submitting flat files.

#### Authorized User

The Authorized User must develop an agreed-upon process and format with the immunization program for submitting flat files. Additionally, the Authorized User must implement a workflow that allows end users to create lists of individuals for inclusion in a Group.

### Implementation Considerations

During implementation, trading partners must discuss the contents of the flat files and determine whether each new flat file should replace or append the contents of the existing Group.

If persistent Groups are desired, the mechanisms for removing individuals from an existing Group must also be addressed.
