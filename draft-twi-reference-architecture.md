---
title: Trustworthy Workload Identity Reference Architecture
abbrev: TWI Reference Architecture
docname: draft-twi-reference-architecture-latest
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

This document uses terms and concepts defined by the WIMSE and RATS architectures, as well as to the terms defined by the Trustworthy Workload Identity Special Interest Gorup at the Confidential Computing Consortium. For a complete glossary,
see {{Section 4 of -rats-arch}} , {{-WIMSE}} & {{-TWISIGCharter}}.

The definitions of terms like Workload Identity, Workload Credential and Workload Provenance match those specified by the TWI SIG Charter {{-TWISIGCharter}}.

Workload:

: {{-WIMSE}} defines 'Workload' as "an instance of software executing for a specific purpose". Here we restrict that definition to the portions of the deployed software and its configuration that are subject to Remote Attestation.


Workload Identifier:

: a stable construct around which Relying Parties can form long-lived Workload authorization policies.

Workload Identity:

: the definition of Workload Identity is identical to the definition of the same term by {{-WIMSE}}: "a combination of three basic building blocks: trust domain, Workload Identifier and identity credentials.

Workload Credential:

: an ephemeral identity document containing the Workload Identifier and a number of additional claims, that can be short-lived or long-lived, and that is used to represent and prove Workload Identity to a Relying Party.

Workload Provenance:

: a unique linkage between a Workload Credential and the trusted entities (such as a vendor, developer, or issuer) responsible for the production and/or the remote attestation of the corresponding Workload.

# Gap Analysis

The following shortcomings were identified by performing a gap analysis of the current WIMSE architecture {{-WIMSE}} against the requirements underlying Trustworthy Workload Identity (TWI). The gap analysis provides a basis for identifying extensions necessary to meet the level of trustworthiness required by Confidential Computing environments.

* Protection of Credentials at runtime and insufficient Runtime Attestation:
  * Risk that credentials in memory could be exposed if a Workload’s execution environment is compromised.
  * Confidential Computing does not trust its hosting environment and relies on each Workload attesting itself.
* Token replay and misuse:
  * Token binding, even if used, is not sufficient unless the secrets underpinning the Workload Credentials are protected from leakage.
* Lack of verifiable workload composition accessible through Credential Provenance
  * When a Relying Party receives a Credential, or when an auditor examines a log of decisions by a Relying Party, it is unable to peform additional checks on the security properties of the Workload or the process involved in creating it, beyond the claims communicated inside the Credential.

# Integration of Confidential Computing into WIMSE

## Secure Key Storage & Cryptographic Operations

With TEEs, a Workload’s private keys and sensitive cryptographic operations (such as signing or validating tokens) can be isolated from the hosting environment, reducing the risk of key leakage even if the hosting environment is compromised. (For instance, the WIMSE token — be it a JWT or an X.509 certificate — can be generated and signed within a TEE, ensuring that the proof-of-possession mechanism remains intact.)

## Enhanced Bootstrapping with Attestation

Strengthening the initial bootstrapping process. A TEE can provide hardware-based attestation that a Workload is running in a secure, isolated environment. This attestation could be used as an additional factor during Workload Credential provisioning, ensuring that only Workloads running in a TEE and matching the Workload Credential issuer's attestation policies receive valid credentials.

[^projects]

[^projects]: might be a bit of a stretch/ too much - it basically would be an extension of the bootstrapping projects described in https://datatracker.ietf.org/doc/html/draft-ietf-wimse-arch-03)

## Protected Credential Exchange

For the credential exchange patterns defined in the WIMSE Credential Exchange draft, Confidential Computing can provide a Trusted Execution Environment in which the exchange logic runs. This ensures that the process of exchanging or re-provisioning credentials is protected against tampering and eavesdropping.

## Mitigating Runtime Compromise

Executing the Workload inside a Trusted Execution Environment can lower the risk that runtime attacks (such as memory scraping or side-channel attacks) can expose critical identity or authentication tokens. For example, a Trusted Execution Environment can be used to securely generate and verify proofs of possession that are important within the WIMSE authentication protocol.

## Cross-domain Trust Enhancement

# TWI Use Cases

TODO: Revisit this section to see if we need it or if we can lean on the section just above this.

Like WIMSE, TWI seeks to associate Identities with Workloads. However, for an Identity to be Trustworthy, a few additional requirements must be met:

1. Isolate Workloads' data-in-use, including their Credentials, from the untrusted hosting environment.
2. Guarantee that a Workload can only utilize Credentials issued to it and that its Credentials cannot leak and be used by unauthorized Workloads.
3. Enable the Relying Parties and other interested parties, such as auditors, to make in-depth run-time and retroactive inquiries into the trustworthiness of the Workload based on its Credentials.

# Core Requirements

The TWI Core Requirements can be located {{-TWISIGReq}}.

For Use Case 1, the Workload MUST run in an isolated, remotely attested TEE. Therefore, the process of obtaining the Credentials MUST involve a Verifier service in addition to the Identity Provider service.

For Use Case 2, the Workload Credentials MUST be strongly bound to the Workload instance to which they are issued and the secrets utilized for authentications to Relying Parties using these Credentials MUST be covered by data-in-use protections in the manner they are procured and utilized.

For Use Case 3, the issued Workload Credentials MUST contain information from which the Provenance of the Workload can be determined.

# Alignment or Synergy with WIMSE Architecture

WIMSE defines an architecture for managing Workload Identity in multi-system environments.

1. The WIMSE Architecture, explains the core concept of Workload Identity in-line with the concept of identity in a TWI world.
2. WIMSE model has a CA/Credential issuer that is responsible with provisioning identity Credentials to the Workload.
TWI requirement is roughly similar in terms of issuing credentials. However the requirements and policies applied when issuing credentials vary as described in the Divergence section below.
3. WIMSE Architecture defines Trust Domain, which is the authority that identifies domain within which the identifier is scoped. TWI Architecture is aligned with this basic building block.

# Extensions to WIMSE Architecture

Trustworthy Workload Identity as detailed in this document proposes the following extensions and changes to the core WIMSE Architecture.

## Workload Isolation

The confidentiality and integrity of a Workload is isolated from the hosting environment and other Workloads.

## Provenance

Existing fields inside X.509 certificates (e.g. a certificate serial number) or Workload Identity Tokens (e.g. the "jti" claim) can be used to unique tie the Workload Credential to the chain of events leading up to its issuance, as a result, no change is necessary.

### Workload Provenance

Workload Provenance can be interrogated to receive the metadata pertaining to Workload, as below.

1. Workload Composition, i.e. a detailed list of components that comprise a Workload. This could be expressed as a Software Bill of Materials expressed in terms of industry standards, like SPDX or CycloneDX <TODO: add references?>.

2. Set of Compliance tests that executed on the Workload.

3. Details of Vendor/SaaS information.

The policy for issuing Credentials may demand the information about the Provenance of the Workload. This requries work in two areas (a) Obtaining the Provenance information about the Workload AND (b) Conveying the provenance information inside the Credential.

#### Obtaining Workload provenance

The Workload Provenance can be made available in a transparent manner, which can be audited and verifiable by independent parties.
While it is a policy of the implementation as to how it obtains the provenance information, the trustworthiness aspect associated to Provenance information can be verified during the runtime trustworthiness assessment of a Workload, through the means of Remote Attestation, at the time of Workload acquiring the Credentials from credential issuer.

#### Integrating Workload Provenance with Credential Issuance

The provenance information can be attached to the Workload credentials using a well-defined protocol.

### Credential Provenance

Credential Provenance is the metadata pertaining to the credential issuance itself, binding the:

* Workload (including [Workload Provenance](#workload-provenance)),
* Verifier (as defined in {{Section 4.1 of -rats-arch}}), including the criteria it applied to the attestation evidence.
* Credential Issuer (((Credential Issuer))) (including its issuance policies effective at the time),

While the Workload Identity remains unchanged for as long as the Workload properties remain stable, a unique Credential Provenance MUST be generated each time a Workload Credential is issued.

Creation of a Credential Provenance record SHOULD additionally be recorded in a transparency log (if in use by the solution), to provide discoverability, immutability and non-repudiation by the issuer.
The transparency log record MAY be encrypted to prevent disclosure of Personally Identifiable Information (PII) or buisness-critical data.

## Workload to Platform binding

## Remote Attestation
Remote Attestation plays a fundamental role in attesting the Workload and ensuring that the Workload is running on a platform that provides required trustwrothiness guarantees.

### Integrating Workload Attestation with Credential Issuance
The Credential Issuer would process the Attestation Results and apply its own Appraisal Policy for Attestation results prior to issuing Credentials.

### Remote Attestation of Composite Workloads

# Security Considerations

<cref>TODO</cref>

# IANA Considerations

It is foreseen that additional CBOR OIDS will be needed for new evidence types. As these new evidence types emerge in later documents IANA will need to issue OIDS to standardize the new evidence definitions across attestation systems.

# Acknowledgements

<cref>TODO</cref>
