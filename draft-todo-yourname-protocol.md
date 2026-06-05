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
title: "TODO - Your title"
abbrev: "TODO - Abbreviation"
category: info

docname: draft-todo-yourname-protocol-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: AREA
workgroup: WG Working Group
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

author:
 -
    fullname: Your Name Here
    organization: Your Organization Here
    email: your.email@example.com

normative:

informative:

...

--- abstract

TODO Abstract


--- middle

# Introduction

The transition to Post-Quantum Cryptography (PQC) is driving the deployment of post-quantum key encapsulation mechanisms (KEMs) in Internet protocols. KEMs are expected to replace or complement traditional Diffie-Hellman key establishment mechanisms in protocols such as TLS, IKE, and other secure communication systems.

This document provides security considerations for NTRU+, a lattice-based KEM derived from the NTRU lattice framework. NTRU+ {{KpqC2025}} was selected as a national post-quantum cryptography standard in the Republic of Korea in early 2025, following a two-year public evaluation process that included open cryptanalysis and security review {{CHH+23}} {{BCG+24}}. NTRU+ is designed to achieve IND-CCA2 security and supports multiple parameter sets targeting different security levels.

NTRU+ can be used in a variety of protocol settings. As a KEM, it can replace or complement ephemeral Diffie-Hellman in authenticated key exchange protocols, including TLS 1.3 {{RFC8446}}, SSH {{RFC4253}}, and IKEv2 {{RFC7296}}. It can also be used in public-key encryption frameworks such as HPKE {{RFC9180}}, where a KEM is used to establish shared secret material between communicating parties.

The purpose of this document is to provide guidance for the safe use of NTRU+ in IETF protocols. The document summarizes security properties, implementation requirements, validation procedures, side-channel considerations, and protocol-relevant caveats that protocol designers and implementers should consider when deploying NTRU+ in practice.


# NTRU+ Overview

## Scheme Description
    
Following NTTRU {{NTTRU}}, NTRU+ operates over a polynomial ring R defined by a cyclotomic trinomial, enabling efficient polynomial multiplication through the Number Theoretic Transform (NTT). 

### Key Generation


### Encapsulation


### Decapsulation



## Parameter Sets


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
