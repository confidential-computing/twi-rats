---
###
# Internet-Draft Markdown Template
#
# Rename this file from draft-todo-yourname-protocol.md to get started.
# Draft name format is "draft-<yourname>-<workgroup>-<name>.md".
#
# For initial setup, you only need to edit the first block of fields.
# Only "title" needs to be changed; delete "abbrev" if your title is short.
# Any other content can be edited, but be careful not to introduce errors.
# Some fields will be set automatically during setup if they are unchanged.
#
# Don't include "-00" or "-latest" in the filename.
# Labels in the form draft-<yourname>-<workgroup>-<name>-latest are used by
# the tools to refer to the current version; see "docname" for example.
#
# This template uses kramdown-rfc: https://github.com/cabo/kramdown-rfc
# You can replace the entire file if you prefer a different format.
# Change the file extension to match the format (.xml for XML, etc...)
#
###
title: "Remote Attestation for Trustworthy Workload Identity"
abbrev: "RATS for TWI"
category: info
ipr: trust200902
area: Security
workgroup: RATS

stand_alone: yes

docname: draft-novak-twi-attestation

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
