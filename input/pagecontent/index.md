### Purpose

The purpose of this document is to assist public health professionals in understanding the capabilities of Bulk FHIR and to promote a unified method across the sector.

This document covers the following topics:
- Policy considerations and implications for implementing Bulk Data Access using FHIR
- Technical concepts and options for developing a supporting FHIR infrastructure
- Expectations for trading partners
- Methods for identifying and grouping individuals
- Approaches for executing bulk queries

This document is produced by the [Helios FHIR Accelerator](https://confluence.hl7.org/display/PH/Helios+FHIR+Accelerator+for+Public+Health+Home) for Public Health [Bulk Data Priority Area](https://confluence.hl7.org/display/PH/Bulk+Data). This group of volunteers examined potential uses of Bulk Data exchange to understand the approaches data exchange partners may adopt and to identify considerations for implementers. While the Bulk Data Priority Area has focused on sharing immunization-related data, the information in this document is also applicable to other public health data-sharing scenarios. Additionally, although this document is developed for the US context, it may be relevant to other regions as well.

The [Bulk FHIR data access](https://hl7.org/fhir/uv/bulkdata/) method facilitates the sharing of data on populations of individuals and is a Priority Area for the Helios FHIR Accelerator for Public Health. This approach allows users of Electronic Health Records (EHR) systems and other electronic platforms to utilize the carefully curated data managed by public health programs. By implementing FHIR Bulk data, authorized users can efficiently retrieve data for defined groups through a standardized and reusable method. This process enables public health systems to generate and transmit essential information quickly while reducing the time and effort needed to respond to data requests in proprietary and custom formats.

This document focuses on providing authorized users access to immunization information maintained by jurisdictional immunization programs. Although the Helios FHIR Accelerator has concentrated on an immunization use case, the Bulk Data method and considerations discussed here are relevant to other data-sharing scenarios critical to public health activities.

### Mechanism for Bulk Query

The mechanics of the FHIR Bulk Data Group Level Export are expected to be executed as outlined in the FHIR Bulk Data Access Implementation Guide. No further customization for the immunization use case is anticipated.

However, the Argonaut FHIR Accelerator, which sponsors the Implementation Guide (IG), is exploring enhancements to the standard. The three work streams under consideration include:
- Optimization of the _typeFilter parameter
- Organizing output files by Patient rather than by resource type
- Enhanced criteria-based group creation

For more details, please refer to the [Argonaut 2024 Bulk Optimize Project](https://confluence.hl7.org/display/AP/Bulk+Optimize) site.


### Summary

The work of the Helios Bulk Data Priority Area, outlined in this document, aims to provide a foundation for immunization programs and Authorized Users to implement this functionality. All interested organizations are invited to join the Helios Priority Area to collaborate on pilot testing and the eventual production exchange of immunization data. To suggest alternative use cases for other data sets, please contact the Leads of the Priority Area to schedule further discussions about your needs.