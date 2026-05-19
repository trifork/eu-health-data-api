This IG defines the API contract for EHR systems under EHDS. The EHR system API is one part of a broader interoperability landscape: Member States operate national and cross-border infrastructure, healthcare providers deploy EHR systems, and EHR systems expose interoperability component capabilities that national and access-service infrastructure can use.

### The EHDS Interoperability Landscape

The diagram below shows the main EHDS actors and the API boundaries between them. Blue boxes represent API or scenario boundaries discussed by this IG.

<div>
  <figure class="figure">
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 816 442" class="figure-img img-responsive img-rounded center-block" style="max-width:100%; height:auto;" role="img" aria-labelledby="ehds-overview-title ehds-overview-desc">
      <title id="ehds-overview-title">EHDS Overview</title>
      <desc id="ehds-overview-desc">Overview diagram of EHDS deployment scenarios with links to related implementation pages.</desc>
      <image href="EHDS-overview.drawio.png" width="816" height="442" preserveAspectRatio="xMidYMid meet" />
      <a href="usecase-cross-border-ncp.html"><title>Cross-Border via NCP</title><rect x="334" y="74" width="170" height="30" fill="transparent" pointer-events="all" /></a>
      <a href="usecase-cross-border-ncp.html"><title>National Contact Point</title><rect x="77" y="118" width="100" height="58" fill="transparent" pointer-events="all" /></a>
      <a href="usecase-cross-border-ncp.html"><title>National Contact Point</title><rect x="675" y="118" width="100" height="58" fill="transparent" pointer-events="all" /></a>
      <a href="member-state-architectures.html"><title>National Interoperability Infrastructure</title><rect x="35" y="140" width="542" height="100" fill="transparent" pointer-events="all" /></a>
      <a href="usecase-cross-org.html"><title>Cross-Organization via National Infrastructure</title><rect x="229" y="182" width="120" height="30" fill="transparent" pointer-events="all" /></a>
      <a href="usecase-health-professional-portal.html"><title>Health Professional Access Service</title><rect x="460" y="146" width="100" height="40" fill="transparent" pointer-events="all" /></a>
      <a href="usecase-health-data-portal.html"><title>Health Data Access Service</title><rect x="460" y="196" width="100" height="40" fill="transparent" pointer-events="all" /></a>
      <a href="usecase-health-data-portal.html"><title>Patient Access</title><rect x="464" y="261" width="96" height="30" fill="transparent" pointer-events="all" /></a>
      <a href="usecase-wellness-app.html"><title>Wellness App Access</title><rect x="469" y="320" width="86" height="58" fill="transparent" pointer-events="all" /></a>
      <a href="usecase-ehr-internal.html"><title>Organization-Internal Exchange</title><rect x="177" y="349" width="75" height="40" fill="transparent" pointer-events="all" /></a>
    </svg>
    <figcaption class="figure-caption"><em>Figure: EHDS Overview</em></figcaption>
  </figure>
  <p></p>
</div>

### EHDS Actors

**Member States** — *Infrastructure obligations*

Member States are responsible for operating the national and cross-border infrastructure that makes EHDS primary-use exchange work. In this landscape, that includes:

- **MyHealth@EU and National Contact Points (NCPs):** Member States connect to the MyHealth@EU network through NCPs for cross-border exchange ([Art. 23](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32025R0327)). NCP-to-NCP exchange is governed by the [NCPeH API specification](https://build-fhir.ehdsi.eu/ncp-api/), not by this IG.

- **Access services:** Member States operate services for patient access and health professional access, including an Electronic Health Data Access Service ([Art. 4](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32025R0327)) and a Health Professional Access Service ([Art. 12](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32025R0327)). These services can act as consumers of the EHR system API; their service requirements are not specified here.

- **National interoperability infrastructure:** Member States define how healthcare providers connect and how EEHRxF data is located, routed, aggregated, authorized, and exchanged nationally. This may include federated query services, central repositories, record locators, patient identity services, and authorization infrastructure. Those national architecture choices are out of scope for this IG, but this IG defines the EHR system API surface that such infrastructure can consume.

**Healthcare providers** — *EHR system deployment*

Healthcare providers deploy one or more EHR systems to support care delivery. They may expose the EHR system API directly, through an organizational gateway, through a vendor-supplied facade, or through a national infrastructure connection. This IG describes those deployment patterns but does not mandate the provider's internal architecture.

**EHR systems** — *EHDS interoperability component capability*

EHR systems are the product-level conformance target for this IG. EHDS [Annex II §2.1–2.6](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32025R0327#anx_II) requires EHR systems to provide and receive priority-category data in EEHRxF format through the European interoperability software component. This IG specifies the FHIR API contract for that capability.

An EHR system may implement the API directly, or the capability may be delivered by an associated gateway or facade that is treated as part of the deployed EHR system capability. The backend implementation can be FHIR-server based, document-centric, XDS/XCA-backed, or hybrid; see [Relationship to XDS/FHIR Document Sharing](background-xds-fhir.html).

**Patient apps and wellness applications** — *Application-facing access*

Patient-facing applications and wellness applications interact with EHDS data through access-service infrastructure. This IG describes read access patterns using the same document and resource API transactions, but patient authentication, SMART App Launch mechanics, EU wallet integration, and patient data submission are outside this IG's scope. See [Wellness App Access](usecase-wellness-app.html) for the current boundary.

### Use Cases

The pages below explain how the EHR system API appears in specific deployment scenarios. Each page identifies the actors involved, the technical flow, and what is deliberately out of scope.

#### Deployment Scenarios

- [**Organization-Internal Exchange**](usecase-ehr-internal.html) — EHR systems within a healthcare provider. Covers direct deployment, organizational gateways, and multi-product facades. Scope: how a healthcare provider presents an EHR system API capability; not the provider's internal product architecture.

- [**Cross-Organization via National Infrastructure**](usecase-cross-org.html) — EHR systems as nodes in Member State infrastructure. Covers federated query and central-repository patterns. Scope: how national infrastructure consumes the EHR system API; not a national infrastructure specification.

- [**Cross-Border via NCP**](usecase-cross-border-ncp.html) — Cross-border exchange through MyHealth@EU and National Contact Points. Scope: how this IG supports the national-side retrieval step; not the NCP-to-NCP API.

#### Access Service Scenarios

- [**Health Professional Access Service**](usecase-health-professional-portal.html) — Health professionals access EEHRxF data through a Member State access service. Scope: HPAS as a consumer of the EHR system API; not HPAS product requirements.

- [**Health Data Access Service**](usecase-health-data-portal.html) — Patients access their own health data through a Member State access service. Scope: HDAS as a consumer of the EHR system API; not patient portal UX, identity proofing, or national consent policy.

- [**Wellness App Access**](usecase-wellness-app.html) — Wellness or personal health applications read patient data through access-service infrastructure. Scope: read access using this IG's API transactions; not patient-submitted write flows.

#### Walkthrough

- [**Retrieve a European Patient Summary**](example-patient-summary.html) — Step-by-step walkthrough of authorization, patient lookup, document query, and document retrieval for a European Patient Summary.
