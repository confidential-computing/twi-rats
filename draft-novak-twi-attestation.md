---
title: Remote Attestation for Trustworthy Workload Identity
abbrev: RATS for TWI
docname: draft-novak-twi-attestation-latest
category: info
ipr: trust200902
area: Security
wg: RATS Working Group
stand_alone: true
submissiontype: IETF
number:
date:

v: 3


keyword:
 - trustworthy workload identity
 - remote attestation
 - stable workload credentials
venue:
 group: RATS
 type: Working Group
 mail: rats@ietf.org

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
  RFC9711: rats-eat
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


--- abstract

Trustworthy Workloads are workloads that operate in environments that provide isolation of data in use.
This document describes how Trustworthy workloads can acquire credentials containing stable identifiers, upon proving the trust in the environments in which they operate via Remote Attestation.

--- middle

# Introduction

As organisations move more workloads into untrusted or shared environments, protection of data in use becomes increasingly important. One way of isolating data in use is Confidential Computing: executing a workload (for example an AI model, database process or financial service) inside a hardware-based, remotely attested Trusted Execution Environment (TEE). Workloads operating in such environments need stable and trustworthy identifiers to communicate over the network to the external world. Often such identifiers are provided to them via Credential Authorities upon ascertaining trust in the environments in which these workloads operate. The standard practice to establish trust in the operating environment is through Remote Attestation.

This draft specifies how a Workload operating in Confidential Computing Environment can obtain trustworthy, stable, and workload-bound credentials using Remote Attestation.

# Conventions and Definitions
{: #definitions }
{::boilerplate bcp14-tagged}

This document uses terms and concepts defined by the WIMSE and RATS architectures, as well as the terms defined by the Trustworthy Workload Identity Special Interest Group at the Confidential Computing Consortium.
For a complete glossary, see {{Section 4 of -rats-arch}} , {{-WIMSE}} & {{-TWISIGDef}}.

The definitions of terms like Trustworthy Workload Identity and Workload Credential match those specified by the TWI SIG Definitions {{-TWISIGDef}}.

Workload:

: {{-WIMSE}} defines 'Workload' as "an instance of software executing for a specific purpose". Here we restrict that definition to the portions of the deployed software and its configuration that are subject to Remote Attestation.

Workload Identifier:

: a stable construct around which Relying Parties can form long-lived Workload authorization policies.

Workload Identity:

: the definition of Workload Identity is identical to the definition of the same term by {{-WIMSE}}: "a combination of three basic building blocks: trust domain, Workload Identifier and identity credentials.

Workload Credential:

: an ephemeral identity document containing the Workload Identifier and a number of additional claims, that can be short-lived or long-lived, and that is used to represent and prove Workload Identity to a Relying Party.

Stable Workload Identity, Stable Authorization Policy:

: a Workload Identity or Authorization Policy is considered Stable if it remains constant in the face of software and hardware changes (updates and rollbacks), so long as those updates and rollbacks are authorized, i.e., comply with the policy of what consitutes the allowed version(s) of the software and hardware in question.

Credential Authority:

: an entity trusted to issue Workload Credentials

Bound Workload Credential:

: a Workload Credential is considered Bound if it can only be used in conjunction with a secret Credential Key that only a Workload authorized for the use of that Key can obtain, either by generating and certifying it, or by retrieving it from a secure Key Store.

Workload Owner:

: an entity tasked with specifying policies concerning what Workload composition is considered valid for the purposes of issuing Workload Credentials

Verifier:

: an entity performing the role of Attestation Verification, as documented in {{Section 4 of -rats-arch}}

# Available Options

When dealing with a client Workload that is running inside a remotely attested Trusted Execution Environment, the goal of having a Relying Party having a stable authorization policy and utilizing industry-standard mechanisms for authorization can be achieved by issuing Credentials in a relying party-friendly format, such as those specified by {{-WIMSE}}. Such Credentials may take the form of x.509 certificates or Workload Identity Tokens (WITs) defined in Section 3.1 of {{-WIMSES2S}}. A Workload can start using the Credential for authentication and authorization once it has two items in its possession: the public portion – the Workload Credential itself, and the secret Credential Key necessary to utilize this Credential.

A Stable authorization policy can only be achieved if Workloads can have Stable identities. The decision about what constitutes a trustworthy Workload and a trustworthy configuration is a composition verification, with multiple entities providing Reference Values for the components they vouch for. For the issued Workload Identity to be Stable in addition to Trustworthy, a mapping must be performed between these Reference Values and the issued Identities. In a typical enterprise, Stable authorization policies are expressed in terms of business- rather than technology-oriented concepts, e.g., "Payroll Application", "Located in Germany", "Cleared for handling Personally Identifiable Information", etc. This contrasts with what RATS has historically thought of as Attestation Results, which may relate to the hardware manufacturer, firmware and software versions, etc.

In some implementations, a Credential is precomputed, and the Credential Key is obtained from a Key Store following successful Remote Attestation. In other implementations, the Workload generates its own Credential Key and uses Remote Attestation to certify it.

Within the RATS Architecture, either of these options can be accomplished in one of two ways:

1. The Attestation Results convey to the attesting Workload both the public Credential and the secret Credential Key.
2. The Attestation Results are encoded in an Entity Attestation Token or EAT {{-rats-eat}}, or a bespoke Verifier-specific format, and can be used by the attesting Workload to obtain a Bound Credential and an associated Credential Key, e.g., by contacting a Credential Authority and/or a Key Store, but without further involving the Verifier.

In either case, the detailed information about the Workload’s composition conveyed to the Verifier using RATS “Evidence” is mapped to Stable, technology-agnostic, business-oriented claims about the Workload.

These two options can be visualised at a high level as:
[^tracked-at] https://github.com/confidential-computing/twi-rats/issues/5

From the Workload's perspective, Variant 2 carries with it an extra network roundtrip (the first roundtrip being the workload exchanging “Evidence” for “Attestation Results”). It is the only option available to the Workload for using existing Verifier implementations that make no changes associated with this proposal. This option does however introduce additional latency and reliability costs inherent in an extra roundtrip.

Variant 1 does not carry with it the extra roundtrip, and thus does not carry the additional performance costs or reliability risks.

Several distinct options are possible. In all cases, the Credential is generated and signed by a Credential Authority. The difference is in how the Workload obtains these Credentials. The main pivots are:

1. Where the Credential Key is generated (Key Source):
    1. Inside the Workload Instance
    2. Inside a secure Key Store such as a Hardware Security Module (HSM), by the Workload Owner
2. Where the Workload gets its Credential from (Credential Source):
    1. The Verifier
    2. The Credential Authority (e.g., a Certificate Authority, a Security Token Service, or similar)
    3. The Workload Owner (via the Control Plane)

Note that it is safe to receive the Credential from an untrusted source such as the Control Plane, because it is public. The only requirement is that the obtained Credential matches the Credential Key, which MUST always be obtained securely and only by an authorized Workload instance.

Further, under pivot 2.i, the order of interactions involved in Credential generation might differ:

1. A Workload invokes the Verifier which collaborates with the Credential Authority to compute and return Credentials, returning these Credentials inside the Attestation Results, or
2. A Workload invokes the Verifier, obtains from it the Attestation Results, and forwards these Attestation Results to the Credential Authority inside a Credential Request to get the Credential.

This set of variants results in several distinct Credential Acquisition Mechanisms (CAMs), some of which are listed in the table below:

| CAM | Key Source | Credential Source | Description |
| :---: | :--- | :--- | :--- |
| A | Workload | Verifier | A Proof-of-Possession of the Credential Key is included in the Evidence submitted by the Workload Instance to the Verifier. The Verifier checks the Evidence, then contacts the Credential Authority to compute a Credential based on this Credential Key and returns it to the Workload Instance as part of Attestation Results. |
| B | Workload | Credential Authority | A Proof-of-Possession of the Credential Key is included in the Evidence submitted by the Workload Instance to the Verifier, and also in the Attestation Results returned by the Verifier. The Workload Instance sends the Attestation Results obtained from the Verifier to the Credential Authority, which computes and returns to the Workload Instance a Credential based on these Attestation Results. |
| C | Workload | Credential Authority | A Proof-of-Possession of the Credential Key is included in a Credential Request submitted by the Workload to the Credential Authority alongside Evidence destined for the Verifier. Credential Authority handles the Credential Request by contacting the Verifier on the Workload's behalf, supplying the Evidence from the Credential Request. The Verifier responds with Attestation Results which the Credential Authority uses to compute a Credential, which it then returns to the Workload. |
| N/A | Workload | Workload Owner | This is not a viable option since a Workload that generates its own Credential Key MUST contact either the Verifier or the Credential Authority to build a Credential for this Key. |
| D | Key Store | Workload Owner | The Credential is generated and handed to the Workload by the Workload Owner. The Workload Owner stores the Credential Key in the Key Store. The Workload obtains the Credential Key from the Key Store after completing Remote Attestation. |

These options are illustrated below with sequence diagrams.

## Mechanism A

~~~ aasvg
{::include CAM_A.txt}
~~~

## Mechanism B

~~~ aasvg
{::include CAM_B.txt}
~~~

## Mechanism C

~~~ aasvg
{::include CAM_C.txt}
~~~

## Mechanisms D

Mechanism D consists of a "Credential Provisioning" phase followed by the "Credential Acquisition" phase.

### Credential Provisioning Phase

~~~ aasvg
{::include CAM_D_Prov.txt}
~~~

### Credential Acquisition Phase

~~~ aasvg
{::include CAM_D.txt}
~~~

# Security Considerations

All communications between entities (Workload to Credential Authority, Workload to Verifier etc) MUST be secured using mutually authenticated, confidential, and integrity-protected channels (e.g., TLS).

In addition to the considerations herein, Verifier, which is a central point of anchor for Trustworthy Workload Identifer MUST follow the security guidance detailed in the "Security and Privacy considerations" as detailed in the RATS Architecture {{Section 11 and Section 12 of -rats-arch}}.

The credential key MUST always be stored securely at all time, for example in a secure element of the underlying platform running the Workload.

# Pivacy Considerations

Remote Attestation of a Workload requires exchange of attestation related messages, for example, Evidence and Attestation Results. This can potentially leak sensitive information about the Workload.

Confidentiality: Encryption could be used to prevent unauthorised parties from accessing sensitive information from Evidence or Attestation Results.
This is crucial in multi-tenant environments.
The Credential Key to be released to a Workload MUST always be encrypted to avoid potential leakage to unauthorised actors.

# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}


The following persons, in no specific order, contributed to the work directly, participated in design team meetings, or provided valuable comments during the review of this document.

Pieter Kasselman (SPIRL), Arieal Feldman (Google), Mateusz Bronk (Intel), Manu Fontaine (Hushmesh Inc.), Benedict Lau (EQTY Lab), Zvonko Kaiser (NVIDIA), David Quigley (Intel), Sal Kimmich (GadflyAI), Alex Dalton (Shielded Technologies), Eric Wolfe (Mainsail Industries), Nicolae Paladi(Canary Bit), Mark Gentry (JPMorgan Chase), Jag Raman (Oracle), Brian Hugenbruch (IBM), Jens Alberts (Fr0ntierX), Mira Spina (MITRE) and John Suykerbuyk.

[^tracked-at]: Tracked at:
