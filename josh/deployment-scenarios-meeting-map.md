# Deployment Scenarios → Meeting Swirl Resolution Map

*How the deployment framing clarifies the contested workgroup topics.*

**Core principle:** Many swirls exist because different reporters are thinking from different deployment contexts. The IG now names those contexts explicitly. The resolution pattern is: "at the EHR API surface (what this IG specifies), the requirement is X; the broader context — national infra, access services — is *described in the use cases but not governed by this IG*."

---

## How Actors Fit Differently Across Scenarios

The same actor labels mean different things depending on which scenario is active:

| Scenario | EHR role | National infra role | Access service role |
|---|---|---|---|
| Org-Internal | Publisher + Access Provider (or just Publisher feeding a gateway) | — | — |
| Cross-Org Federated | **Document Access Provider** (queryable by national infra) | Document Consumer (queries each EHR) | — |
| Cross-Org Central Repo | **Document Publisher only** (submits to national repo) | Document Access Provider (the repo) | — |
| HPAS (HCP access) | Document/Resource Access Provider | Routes query to EHR | Document/Resource Consumer |
| Wellness App | Document/Resource Access Provider | — | Authenticates patient; Consumer |
| Cross-Border NCP | Document Access Provider | Routes via NCP | NCP is the Consumer |

**Why this matters:** In the federated scenario the EHR IS the Access Provider and must support queries. In the central-repo scenario the EHR is only a Publisher — patient lookup and query API may not apply at all. This is why Cauvin (thinking central-repo / national infra) and Johan (thinking federated / Swedish record locator) give contradictory requirements for the same EHR API.

---

## Patient Lookup Swirl

**What happened:** Jose wanted no patient read required; Johan wanted chained identifier search as a transaction option; Charles wanted demographic params; Antoine wanted more PDQm params; Giorgio wanted identifier SHALL → SHOULD; Bas wanted server to declare identifier types.

**How deployment framing resolves it:**

- **Federated** — national infra queries the EHR by identifier. The EHR must support identifier-based lookup (ITI-78). Demographic matching happens at the national EMPI/identity layer, not the EHR.
- **Central Repo** — EHR publishes via ITI-105; no query API needed. Patient lookup is irrelevant from the EHR's perspective.
- **HPAS / Wellness App** — the access service resolves patient identity before calling the EHR. The EHR sees an identifier-scoped request.
- **Cross-Border** — NCP resolves identity at the NCP layer via the Identity IA; the EHR sees an identifier query.

**Landing:** Identifier floor is always right at the EHR API surface — every scenario that reaches the EHR at all arrives with an identifier. `$match` by demographics is a composable capability because demographic resolution is a national infrastructure / access service concern, not an EHR API concern. Member States can mandate `$match` via procurement for deployments that need it at the EHR surface.

- **Jose 56155** (no patient read required): addressed — in many deployments (central repo, HPAS) the EHR never does patient lookup; in federated it does.
- **Johan 56304** (chained identifier search): Vassil's reframe holds — chained search is a post-match optimization, not a $match alternative. On-demand FHIR server with resource access makes this cleaner.
- **Giorgio 56617/56618/56854** (identifier SHALL → SHOULD, demographic identity): identifier SHALL at EHR surface is correct; demographic discovery is a national-infra concern.

---

## Auth Swirl

**What happened:** Giorgio's cluster (56615, 56705, 56838, 56848, 56852) wants SMART SHALL → SHOULD. Andries wants auth-neutral. Reinhard wants IUA/SMART delta table. maelpriour wants mTLS alongside private_key_jwt.

**How deployment framing resolves it:**

- At the **EHR API surface** (what this IG specifies): SMART Backend Services SHALL. This is the auth mechanism between the EHR and whoever is calling it — national infra, HPAS, access service.
- The **access service layer** authenticates patients (eIDAS, EU Wallet, national eID) and healthcare professionals (hospital IDP). That layer is *described in the wellness app and cross-org use cases but not governed by this IG.*
- Giorgio's loosening ask is actually about the access service / national infra layer, not the EHR-to-caller auth. Both can be true simultaneously: SMART Backend SHALL at the EHR endpoint; the access service uses whatever auth the Member State requires before it calls that endpoint.
- Reinhard's 56581 (IUA/SMART delta table) is a valid separate ask — documents the relationship for implementers who know IUA. Doesn't affect the SHALL.
- Andries's auth-neutrality concern: the EHR API is auth-neutral in the sense that the EHR validates tokens; who issues those tokens and how patients authenticate to get them is access-service territory.

**Line to use:** "SMART Backend Services is required at the EHR API surface. How patients, HCPs, and national systems authenticate to obtain those tokens is access service / national infrastructure responsibility — described in the use cases but not governed by this IG."

---

## Write / Push Swirl

**What happened:** Cauvin's 56463/56467/56471 want a write API; Reinhard 56385 says HDAS is not consumer-only; Mikael 56150 wants push acknowledgment.

**Regulatory anchor:** Art 5 patient insertion is "through electronic health data access services or applications linked to those services." The EHDAS is the entry point; the EHR receives EHDAS-routed writes via Annex II §2.2.

**How deployment framing resolves it:**

- **Wellness App use case** explicitly states: Art 5 write is out of scope for this IG; it routes through the access service.
- **Cross-Org use case** describes the EHR receiving authorized data from national infrastructure — the inbound Annex II §2.2 obligation is acknowledged without specifying a direct write API.
- **HDAS / HPAS use cases** describe access services as consumers of this IG's API — HDAS can also act as a Publisher if it submits data on behalf of a patient, using the existing ITI-105 path. Reinhard's point that HDAS is not consumer-only is satisfied: the Document Submission Option already supports inbound publication from any authorized caller.

**Line to use:** "The IG acknowledges the Art 5 insertion right. The EHR's obligation is to accept authorized EHDAS-routed submissions (Annex II §2.2) — the existing Document Submission Option (ITI-105/65) covers this. A direct patient-facing write API is not mandated by the regulation and is out of scope for v1."

---

## Resource Access / IPA Swirl

**What happened:** Andries — IPA bundles SMART, which implies patient-context auth, which the EHR shouldn't be required to implement. Bas May 13 — "IPA has no business on the other EHDS APIs."

**How deployment framing resolves it:**

- IPA defines resource access **patterns** (search parameters, capability statements). The FHIR Server use case in `background-xds-fhir.md` explicitly notes: the FHIR server MAY expose resources via IPA; documents and resources are complementary views.
- SMART App Launch (patient-context token issuance) is access service territory — described in the Wellness App use case but not an EHR conformance requirement.
- "IPA has no business on the other EHDS APIs" — correct framing. Resource access via IPA patterns applies to EHRs that hold discrete resources (FHIR-server deployment). Document-centric systems (central repos, XDS) don't expose individual resources — they serve documents.

---

## Useful lines for the meeting

- "At the EHR API surface, [requirement]. The broader [context] is described in our use cases but not governed by this IG."
- "That's a national infrastructure concern — it varies by Member State, and the IG names it explicitly as out of scope."
- "In a federated deployment, [X]. In a central-repo deployment, [Y]. The IG accommodates both; which one applies is a Member State decision."
- "The access service authenticates [patient/HCP]; the EHR sees an authorized request. How that authorization was established is not specified here."
