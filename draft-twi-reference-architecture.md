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


## Glossary
{: #sec-glossary }

This document uses the following terms:

# TWI Use Cases
Following are some of the use cases demanding Trustworthy Workload Identity.

1. A ML Model Owner (acting as Relying Party) wants to deploy its Workload with a unique identity in a privacy
preserving manner. It needs to ensure that the deployed workload is operating in an environment with certain
security guarantees, such as isolated from cloud hosting environment and can be periodically attested.


2. A Workload operating as a deployed lambda running in an isolated environment.

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

### Workload Provenance


# Security Considerations

<cref>TODO</cref>

# IANA Considerations

## CBOR Tag Registrations


# Acknowledgements


<cref>TODO</cref>
