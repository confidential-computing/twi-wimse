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

--- abstract
Technological advancement coupled with security concerns results in multiple use cases that require data-in-use protections.
In addition, regulatory bodies around the world increasingly require data-in-use protection and privacy enhancing technologies.
As these advanced technologies are being deployed, the challenge is how to identify the workloads and place trust in them?
This draft will address the core issue of managing a Trustworthy Workload Identity meeting Confidential Computing requirements.


--- middle

# Introduction

To be filled

# Conventions and Definitions

{::boilerplate bcp14}

This document uses terms and concepts defined by the WIMSE and RATS architectures, as well as to the terms defined by the Trustworthy Workload Identity Special Interest Gorup at the Confidential Computing Consortium. For a complete glossary,
see {{Section 4 of -rats-arch}} & {{-WIMSE}}. TODO: utilize the link to the TWI SIG charter (https://github.com/confidential-computing/governance/blob/main/SIGs/TWI/TWI_Charter.md) or add its definitions here.


# Glossary
{: #sec-glossary }

This document uses the following terms:

The definitions of terms like Workload Identity, Workload Credential and Workload Provenance match those specified by the TWI SIG Charter: https://github.com/confidential-computing/governance/blob/main/SIGs/TWI/TWI_Charter.md

TODO: Update this section with actual contents from the TWI SIG Charter if necessary.

## Workload Identity

## Workload Credential


# TWI Use Cases
Like WIMSE, TWI seeks to associate identities with workloads. However, for an Identity to be Trustworthy, a few additional requirements must be met:

1. Isolate Workloads' data in use, including their Credentials, from the untrusted hosting environment.
2. Guarantee that a Workload can only utilize Credentials issued to it and that its Credentials cannot leak and be used by unauthorized Workloads.
3. Enable the Relying Parties and other interested parties, such as auditors, to make in-depth inquiries into the trustworthiness of the authenticating Workload.

# Core Requirements
The TWI Core Requirements can be located here. (TODO incorporate this link: https://github.com/confidential-computing/twi/blob/main/TWI_Requirements.md)

For Use Case 1, the Workload MUST run in an isolated, remotely attested Trusted Execution Environment. Therefore, the process of obtaining the Credentials MUST involve a Verifier service in addition to the Identity Provider service.

For Use Case 2, the Workload Credentials MUST be strongly bound to the Workload instance to which they are issued and the secrets utilized for authentications to Relying Parties using these Credentials MUST be covered by data-in-use protections in a manner they are procured and utilized.

For Use Case 3, the issued Workload Credentials MUST contain information from which the Provenance of the workload can be determined.

# Alignment or Synergy with WIMSE Architecture
WIMSE defines an architecture for managing workload identity in multi-system environments.

1. The WIMSE Architecture, explains the core concept of Workload Identity in-line with the concept of identity in a TWI world.

2. WIMSE model has a CA/Credential issuer that is responsible with provisioning identity credentials to the workload.
TWI requirement is roughly similar in terms of issuing credential. However the requirements and policies applied when issuing credentials vary as described in the Divergence section below.

3. WIMSE Architecture defines Trust Domain, which is the authority that identifies domain within which the identifier is scoped. TWI Architecture is aligned with this basic building block.

# Extensions to WIMSE Architecture

Trustworthy Workload Identity as detailed in this document proposes the following extensions and changes to the core WIMSE Architecture.

## Workload Isolation
The confidentiality and integrity of Workload is isolated from the hosting environment and other Workloads.

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


# TWI Reference Architecture
{: #sec-ref-architecture }

This sections describes in detail the Reference Architecture for a Trusted Workload Identity system.

## Assumptions

## Roles in the TWI Eco-System

The following sub-sections describe the various roles that exist in the TWI ecosystem.


### Verifier

### Identity Provider

### Credential Issuer

## Reference Architecture

### Using Remote Attestation to obtain an X.509 Certificate Credential from a Certificate Authority

### Using Remote Attestation to obtain a Credential from an HSM

### Workload Provenance


# Security Considerations

<cref>TODO</cref>

# IANA Considerations

## CBOR Tag Registrations


# Acknowledgements


<cref>TODO</cref>
