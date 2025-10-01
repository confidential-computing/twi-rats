---
title: "Remote Attestation for Trustworthy Workload Identity"
abbrev: "RATS for TWI"
category: info
ipr: trust200902
area: Security
workgroup: RATS

stand_alone: yes

docname: draft-novak-twi-attestation-latest

submissiontype: IETF 
number:
date:

v: 3


keyword:
 - next generation
 - unicorn
 - sparkling distributed ledger
venue:
  group: WG
  type: Working Group
  mail: WG@example.com
  arch: https://example.com/WG
  github: USER/REPO
  latest: https://example.com/LATEST

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
    fullname: Mark Novak
    organization: JP Morgan Chase LLC
    email: mark.novak@outlook.com

    fullname: Yogesh Deshpande
    organization: Arm
    email: Yogesh.Deshpande@arm.com

    fullname: Henk Birkholz
    organization: Franhaufer Inst.
    email: Henk.Birkholz@ietf.contact

normative:

informative:

...

--- abstract
Trusworthy Workloads are workloads that operate in an environment that provide isolation of data in use.
This document describes how Trustworthy workloads can acquire credentials containing stable identifiers, upon providing the trust in the environments in which they operate via means of Remote Attestation. 



--- middle

# Introduction

As organisations move more workloads into untrusted or shared environments, Confidential Computing is becoming increasingly important. In such a system, an application or workload (which could be an AI model, database process or financial service) is executed inside a TEE-protected virtual machine (VM). Worklaods operating in such environments need stable and trustworthy identifiers to communicate to external world over network. Often such identifiers are provided to them via Credential issuers upon ascertaining trust in the environments in which these workloads operate. The standard practice to establish required trust in the operating environment is via the means of Remote Attestation. 

This draft specifies how a Workload operating in Confidential Computing Environment can obtain credentials using Remote Attestation to establish the its trustworthiness.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
