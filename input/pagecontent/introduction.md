### What is the FHIR Bulk Data Approach?

The [Bulk FHIR data access](https://hl7.org/fhir/uv/bulkdata/) approach effectively exchanges large health data sets on populations rather than individuals. This approach is detailed in the FHIR Bulk Data Implementation Guide, developed by the SMART Health IT project at Boston Children’s Hospital's Computational Health Informatics Program. This approach may be applied to a wide variety of public health data sets and a number of different Authorized User types including healthcare providers, payors, and community groups such as schools or community-based program providers. Regardless of the nature of the individual actors involved, the application of the FHIR Bulk Data approach provides a consistent framework for data exchange to minimize the use of bespoke formats. This white paper builds off the FHIR Bulk Data Access Implementation Guide to describe the specific application of this generic approach to public health data sharing needs, using the exchange of immunization data held by jurisdictional programs as a pilot use case. A more detailed description of this approach is provided in the [Technical Concepts](technical.html) section of this document.

### Which Public Health Use Cases Could Utilize Bulk Data?

#### General Use Cases

Many public health programs collect, collate, and curate valuable data sets as part of their mission. This information benefits not only public health but also organizations that interact with both small and large populations to provide healthcare, services, support, and other interventions. By offering authorized users improved access to these data sets, public health can enhance health outcomes and quality of life for community members.

Adopting FHIR-mediated bulk data exchange will promote expanded data sharing while minimizing the time and effort required from both authorized users and public health agencies. Various public health programs serve as data stewards, including healthcare providers, payers, service providers, and community programs. Use cases discussed within the Helios Bulk Data Priority Area include:

- **Vital Records Birth Certification Data:** Useful for public health programs monitoring newborns and providing support and services.
- **Vital Records Death Data:** Enables service providers, such as healthcare organizations and payers, to update records for individuals who have passed away, reducing costs associated with unnecessary resource allocations and alleviating the emotional toll on survivors due to inadvertent communications.
- **Data Exchange Between State and Local Public Health Programs:** Facilitates data sharing for geographically located populations.
- **Public Health Collated Data:** Supports care coordination with Medicaid agencies.

However, the primary use case explored within the Bulk Data Priority Area is the sharing of immunization-related data collected by jurisdictional immunization programs within an Immunization Information System (IIS). This use case is explored more thoroughly below.

#### Immunization-Specific Use Cases

The [Immunization Integration Program’s Bulk Query Toolkit](https://www.himss.org/resources/iip-bulk-query-toolkit) identifies several immunization-specific use cases that can benefit from FHIR bulk data exchange instead of individual queries:
- **Obtain Immunization Records:** Retrieve records for all patients with clinic appointments the following day.
- **Limit Reminder or Recall Communications:** Target communications to patients who are due or overdue for a vaccine dose.
- **Evaluate Clinical Care Quality:** Assess performance measures, such as the Healthcare Effectiveness Data and Information Set (HEDIS).
- **Support Public Health Agency Disease Case Investigations:** Utilize vaccination status, demographic data, and other qualifiers.
- **Evaluate Vaccination Status:** Assess large groups, such as coverage rates for health plans, schools, or emergency first responders.
- **Provide Proof of Vaccination:** Supply consumers with documentation for employment, travel, education, or large events.
- **Evaluate Vaccine Product Effectiveness:** Analyze data after Food and Drug Administration (FDA) and Advisory Committee on Immunization Practices (ACIP) approval.

### Who is Involved?

Four major partner roles are anticipated to be involved in the development and implementation of Bulk Data exchange for immunizations. Later sections of this document will outline the specific expectations and activities required for each role during the development and implementation process. These roles include:
- **Public Health Program**
- **Health Information Technology (HIT) Vendor** for public health systems
- **Authorized User Organization**
- **Authorized User HIT Vendor**

For this document, we will use a jurisdictional immunization program as an example, but similar approaches can apply to other public health programs. In this case, the HIT vendor for the immunization program will be the Immunization Information System (IIS) vendor.
Various organizations may qualify as Authorized Users for the immunization use case, including:
- **Healthcare Providers** (e.g., hospitals and clinics)
- **Payers**
- **Community-Based Organizations** (e.g., schools, community programs)
- **Other Public Health Programs**
    - Within the jurisdiction
    - From neighboring jurisdictions, such as local agencies

The needs and technical capabilities of different types of Authorized Users, even within the same category, can vary significantly. Therefore, not all approaches discussed in this document will be practical for every Authorized User. Implementers must fully understand the needs and capabilities of their trading partners and design an approach that can be effectively implemented on both sides of the exchange.

### What are the Steps in Accessing Bulk Data?

The application of the Bulk Data Access approach by an Authorized User involves several steps, including:
1.	Patient Identification: Establishing patient identification between systems for a significant number of individuals.
2.	Cohort Creation and Maintenance: Creating and maintaining cohorts of individuals.
3.	Use of a FHIR Operation for Bulk Data Exchange: Utilizing a FHIR operation for bulk data exchange.
Note that only the last step is covered in the FHIR Bulk Data Access Implementation Guide, which assumes the existence of an agreed-upon cohort of individuals known by both systems. This guidance document will also explore approaches for identifying individuals at scale and for creating and maintaining cohorts to meet the necessary prerequisites for performing a bulk query.

### What are the Benefits of Bulk Data Access?

Using FHIR-based Bulk Data queries benefits both the data source and the Authorized Users requesting access. In the immunization sector, standard methods for querying and receiving data via HL7 V2 messaging exist, but these methods only support individual patient queries. The V2 standards optimize access on a one-by-one basis, which creates significant operational burdens on the Immunization Information System (IIS) when responding to multiple synchronous queries. This approach is often inefficient for the requesting Health Information Technology (HIT) system.

During the COVID-19 pandemic, immunization programs faced increased requests to pull data in bulk for large populations. Even when the IIS provided data at scale in a standard format, these requests typically relied on custom flat file mechanisms, requiring individual planning, coordination, and custom development, which created bottlenecks. Public health staff often lacked the capacity to respond to all data requests due to competing priorities. However, Bulk FHIR offers a standardized and replicable approach for large data access, similar to how HL7 V2 standardizes clinical access for individuals. This method enables consistent and secure access to data for Authorized Users, transforming immunization programs from isolated data silos into integrated data platforms.

Many Authorized Users, such as healthcare providers and payers, operate across different jurisdictions and request data from multiple sources. Historically, variations by jurisdiction have imposed significant burdens on Authorized Users and their HIT vendors, as each jurisdiction often required customized solutions for data exchange. By uniformly adopting FHIR-based Bulk Data exchange, the national landscape will become more harmonized for developers and implementers.

Additionally, FHIR provides a common data structure for representing immunization data. While extensive use of V2 messaging has promoted consistency across systems, FHIR offers a more accessible view of the immunization data model. By leveraging a Bulk Data query approach, immunization data will maintain a similar structure, regardless of its origin in different systems and architectures. Using FHIR for large-scale data exchange will facilitate the development of national resources and knowledge based on a shared understanding of data structure. Other initiatives, such as the [US Core FHIR Implementation Guide](https://www.hl7.org/fhir/us/core/index.html), [Quality Improvement (QI-Core)](https://hl7.org/fhir/us/qicore/), and the [International Patient Summary](https://hl7.org/fhir/uv/ips/), also utilize FHIR profiles for immunization data, and the use of FHIR for data sharing is expected to grow. The adoption of the Bulk FHIR approach described in this document aligns with these efforts.

The FHIR base standard includes three resources specifically related to immunization data:
- **Immunization Resource:** This resource describes the event of an individual receiving a vaccine, including both current and historical administrations. The complete set of Immunization resources linked to a specific individual constitutes their documented immunization history.
- **ImmunizationEvaluation Resource:** This resource documents the comparison of an immunization event against published guidelines to determine if the event is "valid" according to those guidelines.
- **ImmunizationRecommendation Resource:** Based on the individual's immunization history, evaluation against guidelines, and unique attributes, this resource provides a point-in-time set of recommendations (or forecasts) for the individual.

Depending on the specific use case being implemented, any or all of these resources may be included in the scope of a bulk data query as described in this document. Refer to the Data Payload section for more information.

Many of these benefits will extend beyond the immunization sector into other areas of public health data sharing.
