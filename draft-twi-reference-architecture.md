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

This document uses terms and concepts defined by the WIMSE and RATS architecture. For a complete glossary,
see {{Section 4 of -rats-arch}} & {{-WIMSE}}.


# Glossary
{: #sec-glossary }

This document uses the following terms:

## Workload Identity

## Workload Credential


# TWI Use Cases
Following are some of the use cases demanding Trustworthy Workload Identity.

1. A ML Model Owner (acting as Relying Party) wants to deploy its Workload with a unique identity in a privacy
preserving manner. It needs to ensure that the deployed workload is operating in an environment with certain
security guarantees, such as isolated from cloud hosting environment and can be periodically attested.

2. A Workload operating as a deployed lambda running in an isolated environment.

# Core Requirements
The TWI Core Requirements can be located here. (TO DO Add link here from Mark)

# Alignment or Synergy with WIMSE Architecture
WIMSE defines an architecture for managing workload identity in multi-system environments.

1. The WIMSE Architecture, explains the core concept of Workload Identity in-line with the concept of identity in a TWI world.

2. WIMSE model has a CA/Credential issuer that is responsible with provisioning identity credentials to the workload.
TWI requirement is roughly similar in terms of issuing credential. However the requirements and policies applied for issuing credentials vary as described in the divergence section.

3. WIMSE Architecture, defines Trust Domain, which is the authority that identifies domain within which the identifier is scoped.
TWI Architecture, is aligned with this basic building block.

# Divergence from WIMSE

Trustworthy Workload Identity as detailed in this document has following fundamental divergence from core WIMSE Architecture.

## Workload Confidentiality
The confidentiality and integrity of Workload is isolated from the hosting environment and other workloads.

## Workload Provenance
Workload Provenance is the metadata pertaining to workload, as below.
1. Workload Composition, i.e. a detailed list of components that comprise a workload. This could be expressed as Software Bill of Materials expressed in terms of industry standards, like SPDX or CycloneDX.

2. Details about the Continous Integration (CI) or Continous Delivery (CD) of Workload

3. Set of Compliance tests that executed on the workload.

4. Details of Vendor/SaaS information.

The policy for issuing Credentials may demand the information about the Provenance of the Workload. This requries work in two fundamental areas (a) Obtaining the provenance information about the workload AND (b) Conveying the provenance information inside the Credential.

### Obtaining Workload provenance
The Workload Provenance can be made available in a transparent manner, which can be audited and verifiable by independent parties.
While it is a policy of the implementation as to how it obtains the provenance information, the trustworthiness aspect associated to provenance information can be verified during the runtime trustworthiness assessment of a workload, through the means of Remote Attestation, at the time of workload acquiring the credentials from credential issuer.

### Integrating Workload Provenance with Credential Issuance
The provenance information can be attached to the Workload credentials using a well defined protocol.

## Workload to Platform binding

## Remote Attestation
Remote Attestation plays a fundamental role in attesting the Workload and ensuring that the Workload is running on a platform that provides required trustwrothy guarantees.

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

### Workload Provenance


# Security Considerations

<cref>TODO</cref>

# IANA Considerations

## CBOR Tag Registrations


# Acknowledgements


<cref>TODO</cref>
