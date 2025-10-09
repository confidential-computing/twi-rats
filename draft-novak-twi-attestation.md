---
title: Remote Attestation for Trustworthy Workload Identity
abbrev: "RATS for TWI"
category: info
ipr: trust200902
# area: Security
# workgroup: RATS

stand_alone: yes

docname: draft-novak-twi-attestation-latest

submissiontype: IETF
number:
date:

v: 3


keyword:
 - trustworthy workload identity
 - remote attestation
 - 
venue:
#  group: WG
#  type: Working Group
#  mail: WG@example.com
#  arch: https://example.com/WG
  github: "confidential-computing/twi-rats"
  latest: "https://confidential-computing.github.io/twi-rats/draft-novak-twi-attestation.html"

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
- ins: M. Novak
  name: Mark Novak
  org:  J.P. Morgan Chase
  email: mark.f.novak@jpmchase.com

- ins: Y. Deshpande
  name: Yogesh Deshpande
  org:  Arm
  email: Yogesh.Deshpande@arm.com

- ins:
  name: Henk Birkholz
  org:  Franhaufer Inst.
  email: Henk.Birkholz@ietf.contact


normative:

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
  TWISIGDef:
    -: TWISIGDef
    target: https://github.com/confidential-computing/twi/blob/main/TWI_Definitions.md
    title: Trustworthy Workload Identity (TWI) Special Interest Group — Definitions
    author:
      org: Confidential Computing Consortium Trustworthy Workload Identity SIG
  WIMSES2S:
    -: WIMSES2S
    target: https://datatracker.ietf.org/doc/draft-ietf-wimse-s2s-protocol/
    title: WIMSE Service-to-Service Protocol
    author:
      org: IETF
...

--- abstract
Trustworthy Workloads are workloads that operate in environments that provide isolation of data in use.
This document describes how Trustworthy workloads can acquire credentials containing stable identifiers, upon proving the trust in the environments in which they operate via Remote Attestation.

--- middle

# Introduction

As organisations move more workloads into untrusted or shared environments, protection of data in use becomes increasingly important. One way of isolating data in use is Confidential Computing: executing a workload (for example an AI model, database process or financial service) inside a hardware-based, remotely attested Trusted Execution Environment (TEE). Workloads operating in such environments need stable and trustworthy identifiers to communicate over network to the external world. Often such identifiers are provided to them via Credential issuers upon ascertaining trust in the environments in which these workloads operate. The standard practice to establish required trust in the operating environment is through the means of Remote Attestation.

This draft specifies how a Workload operating in Confidential Computing Environment can obtain trustworthy, stable, and workload-bound credentials using Remote Attestation.


# Conventions and Definitions
{: #definitions }
{::boilerplate bcp14-tagged}

This document uses terms and concepts defined by the WIMSE and RATS architectures, as well as the terms defined by the Trustworthy Workload Identity Special Interest Group at the Confidential Computing Consortium.
For a complete glossary, see {{Section 4 of -rats-arch}} , {{-WIMSE}} & {{-TWISIGDef}}.

The definitions of terms like Workload Identity and Workload Credential and Workload Provenance match those specified by the TWI SIG Definitions {{-TWISIGDef}}.

Workload:

: {{-WIMSE}} defines 'Workload' as "an instance of software executing for a specific purpose". Here we restrict that definition to the portions of the deployed software and its configuration that are subject to Remote Attestation.

Workload Identifier:

: a stable construct around which Relying Parties can form long-lived Workload authorization policies.

Workload Identity:

: the definition of Workload Identity is identical to the definition of the same term by {{-WIMSE}}: "a combination of three basic building blocks: trust domain, Workload Identifier and identity credentials.

Workload Credential:

: an ephemeral identity document containing the Workload Identifier and a number of additional claims, that can be short-lived or long-lived, and that is used to represent and prove Workload Identity to a Relying Party.

Stable Workload Identity:

: a Workload Identity is considered Stable if it remains constant in the face of software and hardware changes (updates and rollbacks), so long as those updates and rollbacks are authorized, i.e., stay within policy of what consitutes the current version of the software and hardware in question.

Bound Workload Credential:

: a Workload Credential is considered Bound if it can only be used in conjunction with a secret Credential Key that only a Workload authorized for the use of that Key can obtain (either by generating and certifying it, or by retrieving it from a secure Key Store).

# Available Options

When dealing with a client Workload that is running inside a remotely attested Trusted Execution Environment, the goal of having a Relying Party having a stable authorization policy and utilizing industry-standard mechanisms for authorization can be achieved by issuing Credentials in a relying party-friendly format, such as those specified by {{-WIMSE}}. Such credentials may take the form of x.509 certificates or Workload Identity Tokens (WITs) defined in Section 3.1 of {{-WIMSES2S}}. A Workload can start using the Credential for authentication and authorization once it has two items in its possession: the public portion – the Workload Credential itself, and the secret Credential Key necessary to utilize this Credential.

A stable authorization policy can only be achieved if Workloads can have Stable identities. The decision about what constitutes a trustworthy Workload and a trustworthy configuration is a composition verification, with multiple entities providing Reference Values for the components they vouch for. For the issued Workload Identity to be Stable in addition to trustworthy, a mapping must be performed between these Reference Values and the issued Identities. In a typical enterprise, stable authorization policies are expressed in terms of business- rather than technology-oriented concepts, e.g., "Payroll Application", "Located in Germany", "Cleared for handling Personally Identifiable Information", etc. This contrasts with what RATS has historically thought of as Attestation Results, which may relate to hardware manufacturer, firmware and software versions, etc.

In some implementations, a Credential is precomputed, and the Credential Key is obtained from a key store following successful Remote Attestation. In other implementations, the Workload generates its own Credential Key and uses remote attestation to certify it.

Within the RATS Architecture, either of these options can be accomplished in one of two ways:

1. The Attestation Results convey to the attesting Workload everything it needs.
2. The Attestation Results are encoded in a proprietary, Verifier-specific format, and can be used by the attesting Workload to obtain an industry-standard Bound Credential and/or an associated Credential Key without further involving the Verifier.

In either case, the detailed information about the workload’s composition conveyed to the Verifier using RATS “Evidence” is mapped to stable technology-agnostic business-oriented claims about the workload.

Variant 2 carries with it an extra roundtrip (the first roundtrip being the workload exchanging “evidence” for “attestation results”). It is the only option available to the attestor for existing Verifier implementations that make no changes associated with this proposal. This option does however introduce additional latency and reliability costs inherent in an extra roundtrip.

Variant 1 does not carry with it the extra round trip and thus does not carry the additional performance costs or reliability risks.

TODO: Insert the "Variant1/Variant2" ASCII art generated from our existing documents.

Several distinct options are possible, outlined below. In all cases, the Credential is generated and signed by the Identity Provider (IDP), but may get to the Workload in one of several ways, described later. The main pivots are:

1. Where the Credential Key is generated:
    1. By the Workload itself
    2. By an Identity Provider as part of Credential generation
2. Where the Workload gets its Credential from:
    1. The Verifier
    2. The IDP
    3. The Control Plane

| No. | Credential Key Generated By | Credential Obtained From | Description |
| ---: | :--- | :--- | :--- |
| 1.i | Workload Instance (Attester) | Verifier | A Proof-of-Possession of the Credential Key is included in Evidence submitted by the Workload to the Verifier. The Verifier contacts the IDP to create a Credential based on this Credential Key and returns it to the Workload Instance as part of Attestation Results. |
| 1.ii | Workload Instance (Attester) | Identity Provider | A Proof-of-Possession of the Credential Key is included in Evidence submitted by the Workload to the Veifier, and also in the Attestation Results returned by the Verifier. The Workload sends the Attestation Results to the IDP, which creates and returns to the Workload Instance a Credential based on these Attestation Results. |
| N/A | Workload Instance (Attester) | Control Plane | N/A |
| 2.i | Identity Provider | Verifier | The Workload generates an asymmetric encryption key and sends its public portion to the Verifier as part of Evidence. The Verifier obtains the Credential and the Credential Key from the IDP or a Key Store and returns these to the Workload, encrypted with the public encryption key. |
| 2.ii | Identity Provider | Identity Provider | The Workload generates an asymmetric encryption key and sends the public portion to the Verifier as part of Evidence. The Verifier includes the public portion of the Encryption Key in the Attestation Results. The Workload sends the Attestation Results to the IDP which returns to it the Credential and the Credential Key encrypted to the public encryption key in the Attestation Results. |
| 2.iii | Identity Provider | Control Plane | The Credential is given to the Workload by the Control Plane. The Workload obtains the Credential Key from the Identity Provider or a Key store using the same mechanism as in the previous row. |

These options are illustrated below with interaction diagrams.

TODO: Generate ASCII art around each of the  sequence diagrams.

## Option 1.i

File: Option_1i.sd

## Option 1.ii

File: Option_1ii.sd

## Option 2.i

File: Option_2i.sd

## Option 2.ii

File: Option_2ii.sd

## Option 2.iii

File: Option_2iii.sd

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
