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
see {{Section 4 of -rats-arch}} & {{-WIMSE}}. TODO: provide a link to the TWI SIG charter or add its definitions here.


## Glossary
{: #sec-glossary }

This document uses the following terms:

# TWI Use Cases
Like WIMSE, TWI seeks to associate identities with workloads. However, for an Identity to be Trustworthy, a few additional requirements must be met:

1. To protect data in use, including its Credentials, the Workload MUST run in an isolated, remotely attested Trusted Execution Environment. Therefore, the process of obtaining the Credentials MUST involve a Verifier service in addition to the Identity Provider service.
2. The Workload Credentials MUST be strongly bound to the Workload instance to which they are issued.
3. The issued Workload Credentials MUST contain information from which the Provenance of the workload can be determined.

# Core Requirements

## Workload composition

## Workload lifespan

##

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
