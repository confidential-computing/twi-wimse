---
title: Trusted Workload Identity Reference Architecture
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
  text-list-symbols: -o*+
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

informative:
  RFC9334: rats-arch
  I-D.draft-ietf-wimse-arch: WIMSE
  TWISIGCharter:
    target: https://github.com/confidential-computing/governance/blob/main/SIGs/TWI/TWI_Charter.md
    title: Trustworthy Workload Identity (TWI) Special Interest Group — Charter
    author:
      org: Confidential Computing Consortium Trustworthy Workload Identity SIG
  TWISIGReq:
    target: https://github.com/confidential-computing/twi/blob/main/TWI_Requirements.md
    title: Trustworthy Workload Identity (TWI) Special Interest Group — Requirements
    author:
      org: Confidential Computing Consortium Trustworthy Workload Identity SIG

--- abstract
This Internet-Draft covers a gap analysis performed by the Confidential Computing Consortium on WIMSE identifying areas where the current WIMSE architecture should be extended to accommodate workloads running in confidential computing environments. It outlines high-level requirements for the these extensions and describes a series of use cases. 

--- middle

# Introduction
Until recently, there were few scenarios demanding data-in-use protections. This is starting to change. Regulatory bodies worldwide are increasingly requiring data-in-use protection and privacy-enhancing technologies. Outside of regulatory requirements, companies are exploring multiparty computation, machine learning training & inferencing, addressing the actual and perceived risks of computing in the public clouds, insider threats, and other reasons for protecting data-in-use. Correspondingly, there is an increased push to harmonize management and governance of human and non-human identities. Enterprises interested in strong assurances around the security of their deployed workloads, for regulatory, contractual and peace of mind reasons, will soon face large and challenging tasks of upgrading their existing IT systems to meet these requirements.
Current ways of issuing and managing workload identities, as well as those required for effective protection of data-in-use, suffer from multiple architectural shortcomings; chief among them:

1. Lack of workload isolation against the hardware and the operating system owners/administrators, as well as peer workload instances
2. Lack of strong binding between a workload credential and the workload instance to which that credential had been issued
3. Inability to associate a credential with a set of decisions leading up to its issuance
_Note that these requirements are related: lack of process isolation eases credential exfiltration and leads to credential leakage and reuse._

In the immediate term, effective Confidential Computing faces a significant challenge: the scale of uplift and immature tooling are both clear barriers. Longer term, however, Confidential Computing provides a vital improvement due to its unique features and broad availability. Confidential Computing-assisted mechanisms have to fit inside the emerging Workload Identity solution ecosystem. The evolution of this ecosystem should remain in alignment with the expectations of the owners and operators of Confidential Computing workloads. These efforts will build on the concept of _Trustworthy Workload Identity_ defined below. Data-in-use protection of workloads that have such identities will be a critical downstream effect.

While it is quite clear that for the foreseeable future this need is not going to be met by Confidential Computing alone, the security features related to workload identification and isolation offered by Confidential Computing eclipse other approaches. Confidential Computing will not be first to market, and thus will need to evolve to fit within, and ideally serve as the North Star of workload identity as it becomes a foundational pillar of trustworthy and governable enterprise computing.

# Conventions and Definitions
{: #definitions }

{::boilerplate bcp14}

This document uses terms and concepts defined by the WIMSE and RATS architectures, as well as to the terms defined by the Trustworthy Workload Identity Special Interest Gorup at the Confidential Computing Consortium. For a complete glossary,
see {{Section 4 of -rats-arch}} , {{-WIMSE}} & {{-TWISIGCharter}}. 

The definitions of terms like Workload Identity, Workload Credential and Workload Provenance match those specified by the TWI SIG Charter.

**Workload** as used in this document restricts the definition of the same term by WIMSE – “a running instance of software executing for a specific purpose” – to just that part of the code and configuration of the (WIMSE-defined) Workload that is subject to Remote Attestation.
**Workload Identifier** is a stable construct around which Relying Parties can form long-lived Workload authorization policies.
**Workload Identity** is defined exactly as it is by WIMSE {{-WIMSE}} – a combination of three basic building blocks: trust domain, Workload Identifier and identity credentials.
**Workload Credential** is an ephemeral identity document containing the Workload Identifier and a number of additional claims, that can be short- or long-lived and which is used to represent and prove Workload Identity to a relying party.
**Workload Provenance** is a linkage between a Workload Credential and a trusted entity (e.g., a vendor, developer, or issuer) responsible for the creation and/or attestation of the corresponding Workload.

# Gap Analysis
An analysis was performed by the Confidential Computing Consortium of the existing WIMSE architecture to identify extensions necessary to meet the level of trustworthiness required by confidential computing environments.

- Protection of Credentials at Runtime, Key Management in Dynamic Environments and Insufficient Runtime Attestation:
  - Risk that credentials in memory could be exposed if a workload’s execution environment is compromised.
  - The current solutions rely on traditional key management practices which may not be good enough against sophisticated runtime attacks.
  - Runtime can still be compromised, even if keys are securely managed, a compromised runtime can still leak. Similarly, a secure runtime is less effective if key management is weak. Integrating both robust key lifecycle management and continuous runtime attestation provides better security.


- Token Replay and Misuse in Cross-domain Scenarios:
Even with proper token binding, the risk of replay attacks or the use of compromised tokens across trust boundaries remains, especially if an attacker manages to intercept tokens during service-to-service exchanges.

# Integration of Confidential Computing into WIMSE
**Secure Key Storage & Cryptographic Operations:**
With TEE’s, a workload’s private keys and sensitive cryptographic operations (such as signing or validating tokens) can be isolated from the host OS. Reducing the risk of key leakage even if the surrounding system is compromised. (For instance, the WIMSE token—be it a JWT or an X.509 certificate—can be generated and signed within a TEE, ensuring that the proof-of-possession mechanism remains intact.)

**Enhanced Bootstrapping with Attestation:**
Strengthening the initial bootstrapping process. A TEE can provide hardware-based attestation that a workload is running in a secure, isolated environment. This attestation could be used as an additional factor during credential provisioning, ensuring that only workloads running in a TEE receive valid credentials. (might be a bit of a stretch/ too much - it basically would be an extension of the bootstrapping projects described in https://datatracker.ietf.org/doc/html/draft-ietf-wimse-arch-03). 

**Protected Credential Exchange:**
For the credential exchange patterns defined in the WIMSE Credential Exchange draft, confidential computing can provide a secure enclave in which the exchange logic runs. This ensures that the process of exchanging or re-provisioning credentials is protected against tampering and eavesdropping. 

**Mitigating Runtime Compromise:**
Incorporating confidential computing within the workload’s execution environment can lower the risk that runtime attacks (such as memory scraping or side-channel attacks) can expose critical identity or authentication tokens. For example, the confidential computing environment can be used to securely generate and verify proofs of possession that are important within the WIMSE authentication protocol.

**Cross-domain Trust Enhancement:**
In multi-cloud and cross-trust-domain interactions, a hardware-rooted attestation from a TEE can serve as an independent trust anchor. This extra layer of verification helps ensure that even if traditional software-based checks fail, the actual workload identity remains trustworthy.

# TWI Use Cases
TODO: Revisit this section to see if we need it or if we can lean on the section just above this.

Like WIMSE, TWI seeks to associate identities with workloads. However, for an Identity to be Trustworthy, a few additional requirements must be met:

1. Isolate Workloads' data-in-use, including their Credentials, from the untrusted hosting environment.
2. Guarantee that a Workload can only utilize Credentials issued to it and that its Credentials cannot leak and be used by unauthorized Workloads.
3. Enable the Relying Parties and other interested parties, such as auditors, to make in-depth inquiries into the trustworthiness of the Workload based on its Credentials.

# Core Requirements
The TWI Core Requirements can be located {{-TWISIGReq}}.

For Use Case 1, the Workload MUST run in an isolated, remotely attested Trusted Execution Environment. Therefore, the process of obtaining the Credentials MUST involve a Verifier service in addition to the Identity Provider service.

For Use Case 2, the Workload Credentials MUST be strongly bound to the Workload instance to which they are issued and the secrets utilized for authentications to Relying Parties using these Credentials MUST be covered by data-in-use protections in a manner they are procured and utilized.

For Use Case 3, the issued Workload Credentials MUST contain information from which the Provenance of the workload can be determined.

# Alignment or Synergy with WIMSE Architecture
WIMSE defines an architecture for managing workload identity in multi-system environments.

1. The WIMSE Architecture, explains the core concept of Workload Identity in-line with the concept of identity in a TWI world.

2. WIMSE model has a CA/Credential issuer that is responsible with provisioning identity credentials to the workload.
TWI requirement is roughly similar in terms of issuing credentials. However the requirements and policies applied when issuing credentials vary as described in the Divergence section below.

3. WIMSE Architecture defines Trust Domain, which is the authority that identifies domain within which the identifier is scoped. TWI Architecture is aligned with this basic building block.

# Extensions to WIMSE Architecture

Trustworthy Workload Identity as detailed in this document proposes the following extensions and changes to the core WIMSE Architecture.

## Workload Isolation
The confidentiality and integrity of a Workload is isolated from the hosting environment and other Workloads.

## Provenance

### Workload Provenance
Workload Provenance is the metadata pertaining to workload, as below.
1. Workload Composition, i.e. a detailed list of components that comprise a workload. This could be expressed as Software Bill of Materials expressed in terms of industry standards, like SPDX or CycloneDX.

2. Details about the Continous Integration (CI) or Continous Delivery (CD) of Workload

3. Set of Compliance tests that executed on the workload.

4. Details of Vendor/SaaS information.

The policy for issuing Credentials may demand the information about the Provenance of the Workload. This requries work in two areas (a) Obtaining the provenance information about the workload AND (b) Conveying the provenance information inside the Credential.

#### Obtaining Workload provenance
The Workload Provenance can be made available in a transparent manner, which can be audited and verifiable by independent parties.
While it is a policy of the implementation as to how it obtains the provenance information, the trustworthiness aspect associated to provenance information can be verified during the runtime trustworthiness assessment of a workload, through the means of Remote Attestation, at the time of workload acquiring the credentials from credential issuer.

#### Integrating Workload Provenance with Credential Issuance
The provenance information can be attached to the Workload credentials using a well-defined protocol.

### Credential Provenance
Credential Provenance is the metadata pertaining to the credential issuance itself, binding the:

- Workload (including [Workload Provenance](#workload-provenance)),

- Verifier (as defined in {{Section 4.1 of -rats-arch}}), including the criteria it applied to the attestation evidence.

- [Credential Issuer](#credential-issuer) (including its issuance policies effective at the time),


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
