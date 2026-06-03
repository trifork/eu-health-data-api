### Overview

The document exchange model in this IG is derived from the IHE MHD Standard. The FHIR `DocumentReference` resource is an index card representing a document: [ITI-67](https://profiles.ihe.net/ITI/MHD/ITI-67.html) finds the cards, [ITI-68](https://profiles.ihe.net/ITI/MHD/ITI-68.html) follows `attachment.url` to fetch the document. The Document Consumer queries, follows the URL, and gets a document of the type and format the card advertises.

How the server (Document Access Provider) stores or builds that document does not change the API contract, but different patterns are discussed on this page as informative examples for implementers.

---

### Backend Implementation Patterns

- **FHIR server** — A FHIR server holding individual FHIR resources may produce FHIR documents on demand by assembling these resources into a FHIR Document (for example, a Patient Summary via `$summary`) or it can serve FHIR documents it has persisted. A FHIR server may also expose the underlying clinical resources directly through [Resource Access](resource-access.html).
- **[Proxy to XDS Infrastructure](https://profiles.ihe.net/ITI/MHD/1336_cross_profile_considerations.html#13361-mhd-actor-grouped-with-xds-infrastructure)** — an MHD layer over an existing XDS/XCA repository of persistent documents; it maps each XDS [`DocumentEntry`](https://profiles.ihe.net/ITI/MHD/32_fhir_maps.html#3451-metadata-object-types-mapped-to-fhir) onto a FHIR `DocumentReference`.

In MHD, the `attachment.url` is opaque, the consumer simply gets it from the [ITI-68](https://profiles.ihe.net/ITI/MHD/ITI-68.html) response. Depending on the deployment, the URL might resolve to:
- An FHIR operation that assembles a document on demand, such as the IPS [`$summary`](https://build.fhir.org/ig/HL7/fhir-ips/OperationDefinition-summary.html) operation (`[base]/Patient/[id]/$summary`)
- A persisted FHIR Document `Bundle` (`[base]/Bundle/[id]`)
- A URL on another system, as when a registry holds only metadata and the document lives at its source

See [Document Exchange](document-exchange.html) for detail on the transactions.

---

### Document Assembly

Whether a document exists before it is requested is the Document Access Provider's choice. MHD defines three modes; either backend can use any of them.

| Mode | Content | `attachment.hash` / `size` | MHD |
|---|---|---|---|
| On-demand | assembled on each request | absent | [On-Demand Documents](https://profiles.ihe.net/ITI/MHD/ITI-67.html#236742212-support-for-on-demand-documents) |
| Delayed | assembled on first request, then kept | `size = 0`, fixed empty hash | [Delayed Document Assembly](https://profiles.ihe.net/ITI/MHD/ITI-67.html#236742213-support-for-delayed-document-assembly) |
| Stable | persisted before the request (MHD default) | present | — |
{: .grid}

An ITI-67 search returns one `DocumentReference` per retrievable document. The mode governs only the *content behind* `attachment.url`: whether it already exists (stable) or is built when fetched (on-demand or delayed). MHD does not require the `DocumentReference` nor the content to exist beforehand; the server may produce either on request.

---

### Conformance

Regardless of backend or assembly mode, a Document Access Provider meets the same API requirements:

- **Query** — it accepts the search parameters defined in the [Document Access Provider CapabilityStatement](CapabilityStatement-EEHRxF-DocumentAccessProvider.html) for ITI-67.
- **Return** — every `DocumentReference` it returns conforms to the [`EehrxfMhdDocumentReference`](StructureDefinition-EehrxfMhdDocumentReference.html) profile.

These fix the shape of the query and its results, not how the server stores or builds documents.


