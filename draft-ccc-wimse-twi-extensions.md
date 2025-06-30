---
title: WIMSE Extensions for Trustworthy Workload Identity
abbrev: Trustworthy WIMSE
docname: draft-ccc-wimse-twi-extensions-latest
category: info
ipr: trust200902
area: Security
workgroup: WIMSE

stand_alone: yes
pi:

  rfcedstyle: yes
  toc: yes
  tocindent: yes
  sortrefs: yes
  symrefs: yes
  strict: yes
  comments: yes
  docmapping: yes

venue:
  github: confidential-computing/twi-wimse
  home: https://confidentialcomputing.io/about/committees/

author:

-
  name: Mark Novak
  org: J.P. Morgan Chase
  email: mark.f.novak@jpmchase.com

-
  name: Yogesh Deshpande
  org: arm
  email: Yogesh.Deshpande@arm.com

-
  name: Henk Birkholz
  org: Fraunhofer SIT
  email: henk.birkholz@ietf.contact

-
  ins: M. Bronk
  name: Mateusz Bronk
  org: Intel Corporation
  abbrev: Intel
  email: mateusz.bronk@intel.com

informative:
  RFC9334: rats-arch
  I-D.draft-ietf-wimse-arch: WIMSE
  TWISIGCharter:
    -: TWISIGCharter
    target: https://github.com/confidential-computing/governance/blob/main/SIGs/TWI/TWI_Charter.md
    title: Trustworthy Workload Identity (TWI) Special Interest Group — Charter
    author:
      org: Confidential Computing Consortium Trustworthy Workload Identity SIG
  TWISIGReq:
    -: TWISIGReq
    target: https://github.com/confidential-computing/twi/blob/main/TWI_Requirements.md
    title: Trustworthy Workload Identity (TWI) Special Interest Group — Requirements
    author:
      org: Confidential Computing Consortium Trustworthy Workload Identity SIG
  NIST_SP_800-218A:
    -: SSDF_GenAI_Profile
    target: https://doi.org/10.6028/NIST.SP.800-218A
    title: "Secure Software Development Practices for Generative AI and Dual-Use Foundation Models: An SSDF Community Profile"
    author:
      org: National Institure of Standards and Technology, U.S. Department of Commerce

--- abstract

This document contains a gap analysis that is the output of the Confidential Computing Consortium identifying areas in the IETF WIMSE WG work where the current WIMSE architecture should be extended to accommodate workloads running in Confidential Computing environments. This document outlines high-level requirements for the these extensions and describes a series of use cases.

--- middle

# Introduction

Until recently, there were few scenarios requiring data-in-use protection. This is starting to change. Regulatory bodies worldwide are increasingly requiring data-in-use protection and privacy enhancing technologies. Outside of regulatory requirements, companies are exploring:

* Multiparty computation
* Machine learning training & inferencing
* Addressing the risks of computing in public clouds and high-risk locations
* Entrusting confidential data to SaaS providers
* Insider threats, and other reasons for protecting data-in-use

Correspondingly, there is an increased push to harmonize management and governance of human and non-human identities.
Modern workloads may operate on their own behalf with their own credentials, or as agents on behalf of other entities with delegated credentials.
Entities interested in strong assurances around the security of their deployed workloads, for regulatory, contractual or peace of mind reasons, are facing large and challenging tasks of upgrading their existing computing system infrastructure to meet these requirements.
Current ways of issuing and managing workload identities, as well as those required for effective protection of data-in-use, are subject to multiple architectural challenges; chief among them:

1. Lack of workload isolation against the hardware and the operating system owners/administrators, as well as peer workload instances
2. Lack of strong binding between a workload credential and the workload instance to which that credential had been issued
3. Lack of verifiable composition of the workload, and inability to associate a credential with a set of decisions leading up to its issuance

It is important to highlight that these shortcomings are related: lack of process isolation eases credential exfiltration and leads to credential leakage and reuse.

Confidential Computing can close these architectural gaps due to its unique features (i.e., verifiable composition, strong workload isolation) and broad availability (i.e., support by all major hardware vendors).
Multiple emerging regulations will mean that customers will be looking to these features and capabilities to satisfy them.

Confidential Computing-assisted mechanisms have to align with the emerging Workload Identity solution ecosystem. Correspondingly, the evolution of the Workload Identity ecosystem should remain in alignment with the expectations of the owners and operators of Confidential Computing workloads.
To address this set of requirements, this document defines and elaborates on the concept of `Trustworthy Workload Identity` (TWI) in the following Sections.

# Conventions and Definitions
{: #definitions }

{::boilerplate bcp14-tagged}

This document uses terms and concepts defined by the WIMSE and RATS architectures, as well as the terms defined by the Trustworthy Workload Identity Special Interest Group at the Confidential Computing Consortium.
For a complete glossary, see {{Section 4 of -rats-arch}} , {{-WIMSE}} & {{-TWISIGCharter}}.

The definitions of terms like Workload Identity, Workload Credential and Workload Provenance match those specified by the TWI SIG Charter {{-TWISIGCharter}}.[^TODO1]

[^TODO1]: TODO: Update the {{-TWISIGCharter}} to match terms in this document.
{: source="Mateusz"}

Workload:

: {{-WIMSE}} defines 'Workload' as "an instance of software executing for a specific purpose". Here we restrict that definition to the portions of the deployed software and its configuration that are subject to Remote Attestation.

Workload Identifier:

: a stable construct around which Relying Parties can form long-lived Workload authorization policies.

Workload Identity:

: the definition of Workload Identity is identical to the definition of the same term by {{-WIMSE}}: "a combination of three basic building blocks: trust domain, Workload Identifier and identity credentials.

Workload Credential:

: an ephemeral identity document containing the Workload Identifier and a number of additional claims, that can be short-lived or long-lived, and that is used to represent and prove Workload Identity to a Relying Party.

Workload Provenance:

: the metadata describing the origin and composition of the Workload instance, as determined at Workload instantiation time.
It remains unchanged for the duration of the Workload’s execution.
:   The definition of Workload Provenance is compatible with the definition of Provenance by {{-SSDF_GenAI_Profile}}: `"Metadata pertaining to the origination or source of specified data"`.

Workload Credential Provenance:

: the metatada about a Workload Credential creation, describing where, when and how it got issued — including the attestation policies effective at credential issuance time.
Its definition is compatible with {{-SSDF_GenAI_Profile}}.

Provenance Binding:

: a unique linkage between a Workload (or Workload Credential) and its corresponding provenance record.  A binding is said to be *strong* if it is anchored to the underlying [Root of Trust (RoT)](https://csrc.nist.gov/glossary/term/roots_of_trust), enabling audit of the integrity of the linkage - typically via attestation. Such a binding is non-repudiable, ensuring that the entity responsible for the Workload or Credential cannot later deny its origin or the integrity of its provenance.


# Gap Analysis

The following shortcomings were identified by performing a gap analysis of the current WIMSE architecture {{-WIMSE}} against the requirements underlying Trustworthy Workload Identity (TWI). The gap analysis provides the basis for identifying extensions necessary to meet the level of trustworthiness required by Confidential Computing environments.

* Protection of Credentials at runtime and insufficient Runtime Attestation:
  * Without data-in-use protection, there is always a risk that Credentials in memory could be exposed if a Workload’s execution environment is compromised.
  * Confidential Computing does not trust its hosting environment and relies on each Workload attesting itself.
* Token replay and misuse:
  * Token binding, even if used, is not sufficient unless the secrets underpinning the Workload Credentials are protected from leakage.
* Lack of verifiable workload composition accessible through Credential Provenance:
  * When a Relying Party receives a Credential, or when an auditor examines a log of decisions by a Relying Party, it is unable to peform additional checks on the security properties of the Workload or the process involved in creating it, beyond the claims communicated inside the Credential.

# Alignment or Synergy with WIMSE Architecture

WIMSE defines an architecture for managing Workload Identity in multi-system environments.

1. The WIMSE Architecture explains the core concept of Workload Identity in-line with the concept of identity in the TWI world.
2. WIMSE model has a CA/Credential issuer that is responsible for provisioning identity Credentials to the Workload. TWI requirement is roughly similar in terms of issuing credentials. However, the requirements and policies applied when issuing Credentials vary as described in the Extensions section below.
3. WIMSE Architecture defines Trust Domain, which is the authority that identifies domain within which the identifier is scoped. TWI Architecture is aligned with this basic building block.

# Extensions to the WIMSE Architecture

Trustworthy Workload Identity as detailed in this document proposes the following extensions and changes to the core WIMSE Architecture.

## Workload Isolation and the Role of the Hosting Environment

Under Confidential Computing, the confidentiality and integrity of a Workload is isolated from the hosting environment and other Workloads. The underlying assumption in the WIMSE architecture that the hosting environment can be fully trusted to establish the identity of a hosted Workload is incompatible with the assumptions of Confidential Computing. Confidential Workloads perform their own Remote Attestation and Workload isolation ensures that the hosting environment cannot interfere with this process.

## Remote Attestation

Under Confidential Computing, Remote Attestation plays a fundamental role in assessing the trustworthiness of the Workload and ensuring that the Workload is running on a platform that provides required security guarantees.

### Integrating Workload Attestation with Credential Issuance

The Credential Issuer would process the Attestation Results from the Verifier and apply its own Appraisal Policy for Attestation results prior to issuing Credentials.

### Remote Attestation of Composite Workloads

Confidential Workloads may be "Composite", i.e., span Trusted Execution Environments yet share a Workload Credential. The details are to be defined at a later time.

## Provenance

To ensure the trustworthiness of a Workload Identity, it is essential to rigorously track the origin and lifecycle of both the Workload and its associated Workload Credentials.
The metadata records intended to capture this information are called *Workload Provenance* and *Workload Credential Provenance*, respectively.

### Workload Provenance

Workload Provenance is determined by the hosting environment (for example, at workload instantiation)[^ProvSourcing] and can be interrogated to receive the metadata pertaining to Workload, as follows:

1. **Workload Composition:**
  A detailed list of components that comprise a Workload.
  This can be represented as a Software Bill of Materials (SBOM) using industry standards such as [SPDX](https://spdx.dev/) or [CycloneDX](https://cyclonedx.org/).
  Additionally, supply chain security frameworks like [SLSA](https://slsa.dev/) and provenance attestation mechanisms such as [in-toto](https://in-toto.io/) can be used to capture and verify the integrity, build process, and origin of each component within the Workload.

2. **Compliance Data:**
  Evidence documenting the execution of compliance tests on the Workload.
  This includes verifiable records of policy evaluations, security scans, assessments, and other compliance-related activities.
  For example, artifacts produced by frameworks conformant with the NIST Secure Software Development Framework (SSDF) may be incorporated.

3. **Vendor/SaaS Metadata:**
  Capture the identity, role, and attestation status of third-party vendors or SaaS providers involved in the Workload.

The level and fidelity of information captured by Workload Provenance is subject to policy of a particular Relying Party and/or Credentials issuer.

#### Obtaining Workload Provenance

Workload Provenance information generally consists of metadata originating from sources external to the hosting environment — such as Software Bill of Materials ([SBOM](https://www.cisa.gov/sbom)) for components supplied by independent vendors.
The mechanisms for acquiring and integrating this information are implementation-specific and may vary according to organizational policy and supply chain practices. [^ProvSourcing]

The Workload Provenance can be made available in a transparent manner, which can be audited and verifiable by independent parties.
While it is a policy of the implementation as to how it obtains the provenance information, the trustworthiness aspect associated to Provenance information can be verified during the runtime trustworthiness assessment of a Workload, through the means of Remote Attestation, at the time of Workload acquiring the Credentials from credential issuer.

[^ProvSourcing]: *(Sourcing the Provenance information is an area that requires industry alignment.)*

### Credential Provenance

Credential Provenance is the metadata pertaining to the Credential issuance itself, binding the:

- Workload Credential (including Workload and [Workload Provenance](#workload-provenance))
- Verifier (as defined in {{Section 4.1 of -rats-arch}}), including the criteria it applied to the attestation evidence
- Credential Issuer (including its issuance policies effective at the time of Credential creation)

While the Workload Identity remains stable for as long as the Workload properties remain unchanged, a unique Credential Provenance MUST be generated each time a Workload Credential is issued.

#### Obtaining Credential Provenance

In most deployments, the Credential Issuer operates inside the hosting environment, and thus the Workload Credential Provenance record is typically generated within that environment.

Creation of a Credential Provenance record SHOULD additionally be recorded in a transparency log (if in use by the solution), to provide discoverability, immutability and non-repudiation by the issuer.
The transparency log record MAY be encrypted to prevent disclosure of Personally Identifiable Information (PII) or buisness-critical data.

### Integrating Provenance with Credential Issuance

Provenance information — encompassing both Workload Provenance and Credential Provenance — SHOULD be associated with Workload Credentials using a well defined protocol[^ProvAssociation].
Given the potential for a single Workload to be issued multiple Credentials (i.e., a `1:N` relationship), the Credential Provenance record SHOULD serve as the primary provenance reference and include linkage to Workload Provenance.
This ensures that all relevant provenance details, including those pertaining to the Workload, can be derived from a single (Credential Provenance) record, simplifying the trust derivation for a Relying Party.

[^ProvAssociation]: *(Standarized formats and protocols for storing/discovering/verifying provenance information may need defining.)*

#### Provenance Binding

**External Binding**:

: The association between provenance information and a Workload Credential MAY be established externally, with the provenance record referencing the Credential.
This allows the provenance record to be managed and retrieved independently, while maintaining a verifiable link to the corresponding Workload Identity, and not requiring any changes to existing Workload Identity Token (WIT) format.
For example, the provenance record can include a hash-based fingerprint of the Workload Credential, or reference a unique field from the Credential — such as the serial number in a X.509 certificate or the "jti" (JWT ID) claim in a WIT.
In this case, the Verifier needs to have an upfront knowledge as to the provenance record's location (for example, a protocol to query for the provenance at the credential issuer or through a well-known centralized provenance store or a transparency log).

**Internal Binding**:

: Alternatively, The provenance record (or a pointer to it) MAY be embedded inside the Workload Credential, for example as an X.509 certificate extension or a separate claim embedded inside a Workload Identity Token, uniquely tying the Workload Credential to the chain of events leading up to its issuance.
Note that in deployment scenarios where Credential Issuer and Provenance Issuer are not the same entity, the independently-issued Provenance record MUST still be strongly bound  to the credential by its creator, making the relationship bidirectional: Credential referencing Provenance via a pointer, Provenance strongly bound to the Credential's non-forgeable unique property.

## Workload to Platform binding

TODO: Issue #27 filed.

# Integration of Confidential Computing into WIMSE

## Secure Key Storage & Cryptographic Operations

With TEEs, a Workload’s private keys and sensitive cryptographic operations (such as signing or validating tokens) can be isolated from the hosting environment, reducing the risk of key leakage even if the hosting environment is compromised. (For instance, the WIMSE token — be it a JWT or an X.509 certificate — can be generated and signed within a TEE, ensuring that the proof-of-possession mechanism remains intact.)

## Enhanced Bootstrapping with Attestation

Strengthening the initial bootstrapping process. A TEE can enable hardware-based Remote Attestation, proving that a Workload is running in a secure, isolated environment. This attestation could be used as an additional factor during Workload Credential provisioning, ensuring that only Workloads running in a TEE and matching the Workload Credential issuer's attestation policies receive valid credentials.

## Protected Credential Exchange

For the Credential exchange patterns defined in the WIMSE Credential Exchange draft, Confidential Computing can provide a Trusted Execution Environment in which the exchange logic runs. This ensures that the process of exchanging or re-provisioning credentials is protected against tampering and eavesdropping.

## Mitigating Runtime Compromise

Executing the Workload inside a Trusted Execution Environment can lower the risk that runtime attacks (such as memory scraping or side-channel attacks) can expose critical identity or authentication tokens. For example, a Trusted Execution Environment can be used to securely generate and verify proofs of possession that are important within the WIMSE authentication protocol.

# Security Considerations

<cref>TODO</cref>

# IANA Considerations

Not applicable for this draft at this time.

# Acknowledgements

The following persons, in no specific order, contributed to the work directly, participated in design team meetings, or provided valuable comments during the review of this document.

Pieter Kasselman (SPIRL), Arieal Feldman (Google), Mateusz Bronk (Intel), Manu Fontaine (Hushmesh Inc.), Benedict Lau (EQTY Lab), Zvonko Kaiser (NVIDIA), David Quigley (Intel), Sal Kimmich (GadflyAI), Alex Dalton (Shielded Technologies), Eric Wolfe (Mainsail Industries), Nicolae Paladi(Canary Bit), Mark Gentry (JPMorgan Chase), Jag Raman (Oracle), Brian Hugenbruch (IBM), Jens Alberts (Fr0ntierX), Mira Spina (MITRE) and John Suykerbuyk.

