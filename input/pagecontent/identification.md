The Group Level Export operation outlined in the FHIR Bulk Data Access Implementation Guide assumes the presence of a Group resource on the FHIR server. This resource either contains a list of individuals within the Group or specifies criteria for identifying relevant individuals. However, the guide does not address how individuals are identified across systems or how Group resources are created and maintained. The Helios Bulk Data community has identified several approaches for patient identification and Group creation and maintenance. The following sections explore these approaches. During the onboarding process, trading partners should select one or more methods that align with their specific use cases.

### Approaches 

- [V2-Mediated (Automatic Group Creation)](v2Automatic.html)
- [V2-Mediated (Dynamic Group Creation)](v2Dynamic.html)
- [FHIR-Mediated (Individual Matching)](FHIRIndividual.html)
- [FHIR-Mediated (Bulk Matching)](FHIRBulk.html)
- [Flat File Mediated Matching](flatfile.html)
- [Reuse of Existing Groups](existing.html)
- [Online User Interface](userInterface.html)
- [FHIR Subscription](subscription.html)