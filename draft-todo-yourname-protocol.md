---

title: "Security Considerations for NTRU+"
abbrev: "NTRU+ Security"
category: info

docname: draft-kim-cfrg-ntruplus-security-considerations-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: Security
workgroup: Crypto Forum Research Group
keyword:

* post-quantum cryptography
* key encapsulation mechanism
* NTRU+
* lattice-based cryptography

venue:
group: CFRG
type: Research Group
mail: [cfrg@irtf.org](mailto:cfrg@irtf.org)
arch: https://mailarchive.ietf.org/arch/browse/cfrg/
github: kjh1311990/ntruplus-security-considerations
latest: https://kjh1311990.github.io/ntruplus-security-considerations/draft-kim-cfrg-ntruplus-security-considerations.html

## author:

```
fullname: Jonghyun Kim
organization: Korea University
email: kjh1311990@gmail.com
```

normative:

informative:
KpqC2025:
title: "KpqC Competition"
author:
- org: KpqC
date: 2025
target: https://www.kpqc.or.kr/

CHH23:
title: "TODO: Full title of the CHH paper"
author:
- ins: TODO
name: TODO
date: 2023
target: https://example.com

BCG24:
title: "TODO: Full title of the BCG paper"
author:
- ins: TODO
name: TODO
date: 2024
target: https://example.com

NTTRU:
title: "NTTRU: Efficient and Secure NTRU-Based KEM"
author:
- ins: TODO
name: TODO
date: TODO
target: https://example.com

...

--- abstract

This document provides security considerations for NTRU+, a lattice-based key encapsulation mechanism (KEM) based on the NTRU lattice framework. It discusses security properties, implementation requirements, validation procedures, side-channel considerations, and protocol-relevant caveats for using NTRU+ in Internet protocols.

--- middle

# Introduction

The transition to Post-Quantum Cryptography (PQC) is driving the deployment of post-quantum key encapsulation mechanisms (KEMs) in Internet protocols. KEMs are expected to replace or complement traditional Diffie-Hellman key establishment mechanisms in protocols such as TLS, IKE, and other secure communication systems.

This document provides security considerations for NTRU+, a lattice-based KEM derived from the NTRU lattice framework. NTRU+ {{KpqC2025}} was selected as a national post-quantum cryptography standard in the Republic of Korea in early 2025, following a two-year public evaluation process that included open cryptanalysis and security review {{CHH23}} {{BCG24}}. NTRU+ is designed to achieve IND-CCA2 security and supports multiple parameter sets targeting different security levels.

NTRU+ can be used in a variety of protocol settings. As a KEM, it can replace or complement ephemeral Diffie-Hellman in authenticated key exchange protocols, including TLS 1.3 {{?RFC8446}}, SSH {{?RFC4253}}, and IKEv2 {{?RFC7296}}. It can also be used in public-key encryption frameworks such as HPKE {{?RFC9180}}, where a KEM is used to establish shared secret material between communicating parties.

The purpose of this document is to provide guidance for the safe use of NTRU+ in IETF protocols. The document summarizes security properties, implementation requirements, validation procedures, side-channel considerations, and protocol-relevant caveats that protocol designers and implementers should consider when deploying NTRU+ in practice.

# NTRU+ Overview

## Scheme Description

Following NTTRU {{NTTRU}}, NTRU+ operates over a polynomial ring (R) defined by a cyclotomic trinomial, enabling efficient computation of the Number Theoretic Transform (NTT).

### Key Generation

TODO Key Generation.

### Encapsulation

TODO Encapsulation.

### Decapsulation

TODO Decapsulation.

## Parameter Sets

TODO Parameter Sets.

# Security Considerations

TODO Security.

# IANA Considerations

This document has no IANA actions.

--- back

# Acknowledgments

{:numbered="false"}

TODO acknowledge.
