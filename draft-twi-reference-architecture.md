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

This document uses terms and concepts defined by the WIMSE and RATS architectures, as well as the terms defined by the Trustworthy Workload Identity Special Interest Group at the Confidential Computing Consortium.
For a complete glossary, see {{Section 4 of -rats-arch}} , {{-WIMSE}} & {{-TWISIGCharter}}.

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

Existing fields inside X.509 certificates (e.g., a certificate serial number) or Workload Identity Tokens (e.g., the "jti" claim) can be used to uniquely tie the Workload Credential to the chain of events leading up to its issuance; as a result, no change is necessary.

### Workload Provenance

Workload Provenance can be interrogated to receive the metadata pertaining to Workload, as follows:

1. Workload Composition, i.e. a detailed list of components that comprise a Workload. This could be expressed as a Software Bill of Materials expressed in terms of industry standards, like SPDX or CycloneDX <TODO: add references?>.

2. Set of Compliance tests that executed on the Workload.

3. Details of Vendor/SaaS information.

The policy for issuing Credentials may demand the information about the Provenance of the Workload. This requries work in two areas (a) Obtaining the Provenance information about the Workload AND (b) Conveying the provenance information inside the Credential.

#### Obtaining Workload Provenance

The Workload Provenance can be made available in a transparent manner, which can be audited and verified by independent parties.
While it is a policy of the implementation as to how it obtains the Provenance information, the trustworthiness aspect associated with Provenance information can be verified during the runtime trustworthiness assessment of a Workload, through the means of Remote Attestation, at the time of Workload acquiring the Credentials from credential issuer.

#### Integrating Workload Provenance with Credential Issuance

The Provenance information can be attached to the Workload credentials using a well-defined protocol.

### Credential Provenance

Credential Provenance is the metadata pertaining to the Credential issuance itself, binding the:

* Workload (including [Workload Provenance](#workload-provenance))
* Verifier (as defined in {{Section 4.1 of -rats-arch}}), including the criteria it applied to the attestation evidence
* Credential Issuer (((Credential Issuer))) (including its issuance policies effective at the time)

While the Workload Identity remains stable for as long as the Workload properties remain unchanged, a unique Credential Provenance MUST be generated each time a Workload Credential is issued.

Creation of a Credential Provenance record SHOULD additionally be recorded in a transparency log (if in use by the solution), to provide discoverability, immutability and non-repudiation by the issuer.
The transparency log record MAY be encrypted to prevent disclosure of Personally Identifiable Information (PII) or buisness-critical data.

## Workload to Platform binding

### Scope and Initiation

Platform binding in the context of TWI refers to the association of Workloads with specific hosting environment (platform) characteristics including its Trusted Computing Base (TCB) security properties as well as related attributes, such as geolocation.
Platform binding increases the assurance that Workloads are executed in an appropriate trusted execution environment (TEE), thereby maintaining the integrity and confidentiality of the data processed and allowing Relying Parties to construct and apply security policy, for example, attributes of the hosting environment that relate to data sovereignity.

### Strength of Platform Binding [^todo-applicability]

The association between a Workload and its hosting platform characteristics can achieve different security objectives.
Achievable objectives depend on the capabilities of the platform as well as the instrumentation provided by the Workload, for example, by a Workloads Paravisor.

#### Platform Binding Trust Components

Platform Binding strength depends on the number of components comprising the TCB ("size or scope of the TCB").

The TWI architecture intends to support both:

1. Workloads and Operating Systems that are ***TWI-aware***: entities that have been purpose-built with TWI compatibility in mind.
Such entities can call into hardware-based platform attestation primitives directly (bypassing the need for brokerage, thus minimizing the number of components that need to be trusted).
In this document, such entities are referred to as ***enlightened*** Workloads.
2. "Legacy" Workloads and Operating Systems that operate on confidential computing-enabled hosts (often unaware of it), and do not have embedded attestation facilities. 
Such entities are conduct remote attestation via the aid of platform-provided facilities and levels of abstraction (e.g., a virtual TPM).
In this document, such entities are referred to as ***unenlightened*** Workloads.

For example, for a Workload running in a confidential virtual machine (CVM), the following Platform binding TCBs levels can be (with TCB_L1 being the smallest and TCB_L4 the largest).

```
+--------------------------+----------------------------+
|                          |  host CVM operating system |
|                          +--------------+-------------+
|                          | unenligtened | enlightened |
+----------+---------------+--------------+-------------+
|          | unenlightened |    TCB_L1    |   TCB_L3    |
| workload +---------------+--------------+-------------+
|          | enlightened   |    TCB_L2    |TCB_L4 (best)|
+----------+---------------+--------------+-------------+
```

#### Supporting Definitions [^defs]
[^defs]: maybe hoist these and merge into definition Section of the document above?
{: source=" - Mateusz & Henk"}

Enlightened Workload:

: Exposes direct remote attestation primitives (e.g., nonce inputs) and can call into the host's remote attestation facilities on its own.

Unenlightened Workload:

: Lacks first-class support for remote attestation so that Evidence is generated indirectly by the hosting platform instead (and may lack some insight into a confidential Workload).

Enlightened OS:

: Exposes hardware remote attestation primitives directly to Workloads, without intermediary layers, e.g., via a virtual TPM.

Unenlightened OS:

: Exposes hardware remote attestation primitives indirectly via of additional layers, e.g., a paravisor providing a vTPM interface.

### Platform Binding Freshness

The assurance of freshness in platform binding is crucial to prevent replay attacks and to improve the trustworthiness the remote attestation process.
The following methods describe different levels of platform binding freshness.

| Freshness level | Freshness type    | Description |
| :-------------: | ----------------- | -------- |
| **`AF_L0`**     | None              | No replay protection or liveness challenge, but man-in-the-middle (MiTM) attacks are still thwarted. |
| **`AF_L1`**     | Epoch/time-based  | Relying on clock synchronization and trusted timestamps or epochs. Merkle trees can be used to provide proof of freshness. |
| **`AF_L2`**      | Nonce-based      | Nonce-based freshness, typically initiated from the relying party (RP) or identity provider (IdP) side. |

### Platform Binding Claims

Platform Claims may be inherent to the platform TCB, for example, the security properties of the firmware and software stack running the workload, but also externally-sourced, such as the server's physical location.
Moreover, depending on the Relying Party needs, there can be requirements for varying levels of "stickiness" of Workload Identity to some physical properties of a host.
For example, some Relying Parties may be sensitive to a Workload being migrated to a different hosting platform, while other Workloads may permit such migration as long as it occurs within the same country.
These cases are further described in the following Sections.

#### Platform Claim Fidelity and Sourcing 

The following Platform Claim types are supported:
| Claim fidelity[^term] level | Claim type    | Description |
| :-------------------------: | ----------------- | -------- |
| `CF_L0`      | Declaratory        | Input by the operator (or an untrustworthy device), with the hosting platform upholding its further integrity and non-repudiation, allowing for external audit. For example, manually-entered geolocation or a system's hardware BOM.  |
| `CF_L1`      | External-Attested  | Provided by attested claim providers, such as physical GPS modules connected and attested using protocols like [SPDM](https://www.dmtf.org/standards/spdm)/[TDISP](https://pcisig.com/tee-device-interface-security-protocol-tdisp).       |
| `CF_L2`      | Built-In           | Provided by the confidential computing platform itself and part of its TCB. For example, hardware-issued measurement of running hardware and software.             |

[^term]: Or trustworthiness?
{: source=" - Mateusz & Henk"}

#### Levels of "Stickiness" of Platform Claims to Workload Identity
To support varying needs of Relying Parties to craft policies for different workload migration scenarios, TWIs intend to support the following levels of "stickiness" of the platform binding. 

| Claim Stickiness | Stickiness Type    | Description |
| :-------------------------: | ----------------- | -------- |
| `CS_L1`      | Same TCB           | The binding holds for as long as the platform TCB (hardware/software configuration, including patch levels etc.) is not changing. |
| `CS_L2`      | Same Claims        | The workload identity is not changing for as long as the declared platform claims are the same. Fidelity of this stickiness type depends on the claim set. For example, workload migrated between racks in the same datacenter is not a change if the rack ID is not part of the claims, but move to a datacenter in a different city is.                 |
| `CS_L3`      | Pinpoint-Accurate  | Binding to the concrete host/platform identifier (any type of Workload migration invalidates the binding and thus the TWI)        |

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

[^todo-applicability]: Does this really apply to platform binding or rather remote attestation? Perhaps this section needs to move to attestation instead of platform binding?
{: source=" - Mateusz"}