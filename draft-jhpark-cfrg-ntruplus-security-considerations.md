---
title: "NTRU+ Security Considerations"
abbrev: "NTRU+ Security Considerations"
category: info

docname: draft-jhpark-cfrg-ntruplus-security-considerations-latest
submissiontype: IRTF
number:
date:
consensus: true
v: 3
area: "IRTF"
workgroup: "Crypto Forum"
keyword:
 - NTRU+
 - post-quantum cryptography
 - key encapsulation mechanism
venue:
  group: "Crypto Forum"
  type: "Research Group"
  mail: "cfrg@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/cfrg"
  subscribe: "https://mailman.irtf.org/mailman/listinfo/cfrg"
  github: "jhpark-prof/ntruplus-security-considerations"
  latest: "https://jhpark-prof.github.io/ntruplus-security-considerations/draft-jhpark-cfrg-ntruplus-security-considerations.html"

author:
 -
    fullname: Jong Hwan Park
    organization: Sangmyung University
    email: jhpark@smu.ac.kr
 -
    fullname: Jonghyun Kim
    organization: Korea University
    email: yoswuk@korea.ac.kr
 -
    fullname: MinKyu Shin
    organization: ITCEN PNS
    email: mkshin@itcen.com

normative:

informative:
  KpqC2025:
    title: "KpqC Competition Second-Round Final Results Announcement"
    author:
      -
        org: KpqC
    date: 2025
    target: "https://www.kpqc.or.kr/contents/03_exhibit/board.html?board_id=board_competition&cate=&mode=view&no=9"

  KP26:
    title: "NTRU+: Compact Construction of NTRU Using Simple Encoding Method"
    author:
      -
        name: Jonghyun Kim
      -
        name: Jong Hwan Park
    date: 2026
    target: "https://www.kpqc.or.kr/images/pdf2/NTRU%2B.pdf"

  CHH23:
    title: "Report on evaluation of KpqC candidates"
    author:
      -
        name: Jolijn Cottaar
      -
        name: Kathrin Hövelmanns
      -
        name: Andreas Hülsing
      -
        name: Tanja Lange
      -
        name: Mohammad Mahzoun
      -
        name: Alex Pellegrini
      -
        name: Alberto Ravagnani
      -
        name: Sven Schäge
      -
        name: Monika Trimoska
      -
        name: Benne de Weger
    date: 2023
    target: https://eprint.iacr.org/2023/1853

  BCG24:
    title: "Report on evaluation of KpqC Round-2 candidates"
    author:
      -
        name: Daniel J. Bernstein
      -
        name: Jolijn Cottaar
      -
        name: Emanuele Di Giandomenico
      -
        name: Kathrin Hövelmanns
      -
        name: Andreas Hülsing
      -
        name: Mikhail Kudinov
      -
        name: Tanja Lange
      -
        name: Mairon Mahzoun
      -
        name: Matthias Meijers
      -
        name: Alex Pellegrini
      -
        name: Alberto Ravagnani
      -
        name: Silvia Ritsch
      -
        name: Sven Schäge
      -
        name: Tianxin Tang
      -
        name: Monika Trimoska
      -
        name: Marc Vorstermans
      -
        name: Fiona Johanna Weber
    date: 2024
    target: https://eprint.iacr.org/2024/2077

  LS19:
    title: "NTTRU: Truly Fast NTRU Using NTT"
    author:
      -
        name: Vadim Lyubashevsky
      -
        name: Gregor Seiler
    date: 2019
    target: https://tches.iacr.org/index.php/TCHES/article/view/8293

  APS15:
    title: "On the concrete hardness of Learning with Errors"
    author:
      -
        name: Martin R. Albrecht
      -
        name: Rachel Player
      -
        name: Sam Scott
    date: 2015
    target: https://eprint.iacr.org/2015/046

  DHK22:
    title: "A Thorough Treatment of Highly-Efficient NTRU Instantiations"
    author:
      -
        name: Julien Duman
      -
        name: Kathrin Hövelmanns
      -
        name: Eike Kiltz
      -
        name: Vadim Lyubashevsky
      -
        name: Gregor Seiler
      -
        name: Dominique Unruh
    date: 2023
    target: https://link.springer.com/chapter/10.1007/978-3-031-31368-4_3

  KCP26:
    title: "Accelerating NTRU+ Key Generation via Hierarchical Batch Inversion"
    author:
      -
        name: Jonghyun Kim
      -
        name: Haehyun Cho
      -
        name: Jong Hwan Park
    date: 2026
    target: https://eprint.iacr.org/2026/1191

  UXT21:
    title: "Curse of Re-encryption: A Generic Power/EM Analysis on Post-Quantum KEMs"
    author:
      -
        name: Rei Ueno
      -
        name: Keita Xagawa
      -
        name: Yutaro Tanaka
      -
        name: Akira Ito
      -
        name: Junko Takahashi
      -
        name: Naofumi Homma
    date: 2021
    target: https://tches.iacr.org/index.php/TCHES/article/view/9298



...

--- abstract

This document describes security considerations for the use of NTRU+ in
Internet protocols.  NTRU+ is a lattice-based key encapsulation mechanism
(KEM) based on the NTRU framework and designed to provide IND-CCA2 security.
The document summarizes the scheme structure and parameter sets, and discusses
implementation and protocol considerations including key-generation rejection
sampling, input validation, pairwise consistency testing, explicit rejection
behavior, randomness requirements, and side-channel leakage during
decapsulation.  It is intended to help protocol designers and implementers use
NTRU+ safely in settings such as authenticated key exchange, public-key
encryption, and KEM-based authentication.


--- middle

# Introduction

The transition to post-quantum cryptography (PQC) is driving the deployment of post-quantum key encapsulation mechanisms (KEMs) in Internet protocols. KEMs are expected to replace or complement traditional Diffie-Hellman key establishment mechanisms in protocols such as TLS, IKE, and other secure communication systems.

This document provides security considerations for NTRU+, a lattice-based KEM derived from the NTRU lattice framework. NTRU+ {{KP26}} was selected as one of the final KEM algorithms in the Korean post-quantum cryptography competition in January 2025 {{KpqC2025}}, following a two-year public evaluation process that included open cryptanalysis and security review {{CHH23}} {{BCG24}}. NTRU+ is designed to achieve IND-CCA2 security and supports multiple parameter sets targeting different security levels.

NTRU+ can be used in a variety of protocol settings. As a KEM, it can replace or complement ephemeral Diffie-Hellman in authenticated key exchange protocols, including TLS {{?RFC8446}}, SSH {{?RFC4253}}, and IKE {{?RFC7296}}. It can also be used in public-key encryption frameworks such as HPKE {{?RFC9180}}, where a KEM is used to establish shared secret material between communicating parties.

The purpose of this document is to provide guidance for the safe use of NTRU+ in IETF protocols. The document summarizes security properties, implementation requirements, validation procedures, side-channel considerations, and protocol-relevant caveats that protocol designers and implementers should consider when deploying NTRU+ in practice.


# NTRU+ Overview

## Scheme Description

Following Lyubashevsky and Seiler {{LS19}}, NTRU+ operates over the polynomial ring `R_q = Z_q[x]/<Phi_{3n}(x)>`, where `Phi_{3n}(x) = x^n - x^{n/2} + 1` is a cyclotomic trinomial and q denotes the coefficient modulus. The use of a cyclotomic trinomial enables efficient polynomial multiplication through the Number Theoretic Transform (NTT). Secret polynomials are sampled according to a centered binomial distribution (CBD), where each coefficient is generated by subtracting one uniformly random bit from another, resulting in values in {-1, 0, 1}.

NTRU+ uses three hash functions, denoted by F, G, and H, which are instantiated with SHAKE-256 in {{KP26}}.

### Key Generation

The key generation algorithm (see Section 6.3.1 of {{KP26}}) internally samples independent 32-byte random seeds, which SHOULD be generated by an approved random bit generator (RBG), and produces a public key pk and a private key sk. The seeds are used to generate short polynomials f' and g', respectively, whose coefficients are sampled according to the CBD.

Key generation employs rejection sampling to ensure that both polynomials f=3f'+1 and g=3g' are invertible in the ring R. If a generated polynomial f is not invertible, a new 32-byte seed is used to generate a replacement polynomial. The same procedure is applied to g.

The public key consists of the polynomial h represented in NTT form. The private key consists of (f, h^{-1}, F(pk)), where both f and h^{-1} are stored in NTT form and F(pk) denotes a hash of the public key. After key generation is completed, the random seeds used to generate f and g SHOULD be securely erased.

The NTRU+ key-generation algorithm does not include a Pairwise Consistency Test (PCT). Although a PCT could be incorporated as an optional self-test by extending the key-generation procedure, it is not part of the current specification. This document therefore considers only the standard key-generation procedure defined in {{KP26}}.

### Encapsulation

The encapsulation algorithm (see Section 6.3.1 of {{KP26}}) internally samples an n-bit random message m. The message SHOULD be generated using an approved RBG. The algorithm outputs a ciphertext c and a 32-byte shared secret K.

The shared secret K and an intermediate randomness ρ are derived as (K, ρ) := H(m, F(pk)). The inclusion of F(pk) in this derivation is intended to provide resistance against multi-target attacks.

Using simplified notation, a short polynomial r is generated from ρ by the CBD sampling procedure, written as r := CBD(ρ). The encoded message polynomial is computed as M := Encode(m, G(r)) using the semi-generalized one-time pad (SOTP) operation, which is designed so that the coefficients of M follow the same CBD distribution. The ciphertext is then computed as c = hr + M. The resulting ciphertext polynomial is computed in NTT form and serialized for transmission by encoding its coefficients as 12-bit values. During decapsulation, the serialized ciphertext is decoded back into its NTT-domain representation and used directly as input to the decapsulation algorithm.

After encapsulation is completed, the n-bit random message SHOULD be securely erased.

### Decapsulation

The decapsulation algorithm (see Section 6.3.1 of {{KP26}}) takes as input a ciphertext c and the private key sk = (f, h^{-1}, F(pk)). The algorithm outputs either a 32-byte shared secret K for a valid ciphertext or a decapsulation error for an invalid ciphertext. Thus, NTRU+ employs explicit rejection for invalid ciphertexts rather than deriving and returning a pseudorandom key.

The algorithm SHOULD first verify that the ciphertext c is properly formed. In particular, each coefficient of the ciphertext polynomial MUST be checked to ensure that it lies within the valid range defined by the modulus q. If this validation fails, the algorithm MUST return a decapsulation error.

Otherwise, a candidate encoded message polynomial M is first recovered from c using f. A candidate randomness polynomial r is then recovered from c and M using h^{-1}. Using simplified notation, the SOTP decoding operation computes m' := Decode(M, G(r)), yielding either an n-bit message m' or a decoding failure ⊥.

The candidate value m', together with the stored public-key hash F(pk), is used to derive both a candidate shared secret K and an intermediate randomness ρ' as (K, ρ') := H(m', F(pk)). A regenerated polynomial r' is then computed as r' := CBD(ρ').

Subsequently, two validation checks are performed. The first verifies that the SOTP decoding completed without error. The second verifies that the recovered randomness polynomial r matches the regenerated polynomial r'. Only if both checks succeed is the shared secret K accepted; otherwise, the decapsulation algorithm returns a decapsulation error.

To avoid creating an error oracle, implementations SHOULD perform both validation checks unconditionally and combine their results before making a single acceptance or rejection decision. Implementations SHOULD NOT reveal which validation check failed.

Upon completion of decapsulation, all intermediate values, including recovered polynomials, messages, randomness values, and candidate shared secrets, SHOULD be securely erased.

## Parameter Sets

NTRU+ provides three parameter sets: NTRU+768, NTRU+864, and NTRU+1152. Table 1 summarizes the ring parameters and the sizes of the cryptographic material associated with each parameter set, together with the estimated classical security levels obtained using the Lattice Estimator {{APS15}}.

| Parameter | n | q | pk | sk | ct | ss | security |
|---|---:|---:|---:|---:|---:|---:|---:|
| NTRU+768 | 768 | 3457 | 1152 | 2336 | 1152 | 32 | 156 |
| NTRU+864 | 864 | 3457 | 1296 | 2624 | 1296 | 32 | 179 |
| NTRU+1152 | 1152 | 3457 | 1728 | 3488 | 1728 | 32 | 248 |
{: title="Parameter-set ring parameters, sizes, and estimated classical security levels"}

In Table 1, n is the polynomial degree, q is the coefficient modulus, pk = public key, sk = private key, ct = ciphertext, and ss = shared secret. Key, ciphertext, and shared-secret sizes are given in bytes. Security levels are given in bits.

Table 2 summarizes end-to-end single-core performance measurements of the NTRU+ KEM API. Measurements were taken on an Intel Core i7-8700K CPU @ 3.70GHz on Linux/x86_64 using clang 18.1.3 with -O3. Each benchmark was pinned to a single CPU core and measured for 10 seconds per operation. Values are rounded to the nearest operation per second.

| Impl. | Parameter | KeyGen | Encap | Decap |
|---|---|---:|---:|---:|
| Optimized C | NTRU+768 | 41,225 | 53,641 | 47,787 |
| Optimized C | NTRU+864 | 37,519 | 46,834 | 41,458 |
| Optimized C | NTRU+1152 | 24,858 | 36,333 | 31,108 |
| AVX2 | NTRU+768 | 138,191 | 120,052 | 196,101 |
| AVX2 | NTRU+864 | 125,406 | 102,974 | 154,965 |
| AVX2 | NTRU+1152 | 84,452 | 81,498 | 123,876 |
{: title="Single-core end-to-end KEM API performance"}

Key generation and encapsulation include randomness generation performed by the implementation.


# NTRU+ Security Considerations

## Correctness and Security Properties

Historically, achieving negligible worst-case correctness error has been a significant challenge in NTRU-based public-key encryption schemes. In classical NTRU encryption, an adversary may construct ciphertexts of the form c = hr + m by choosing r or m maliciously, making it difficult to achieve negligible worst-case correctness errors for all possible ciphertexts.

To address this issue, NTRU+ employs two techniques. First, the polynomial r is deterministically derived through the Fujisaki-Okamoto (FO) transform. Second, the polynomial m is generated through the SOTP encoding procedure. As a result, an adversary no longer has direct control over the values of r and m appearing in honestly generated ciphertexts.

This result builds upon {{DHK22}} and extends the analysis to the CBD sampling used in NTRU+, where all coefficients of r and m are sampled according to the CBD. Consequently, the worst-case correctness error of NTRU+ is effectively identical to the average-case correctness error over honestly generated ciphertexts.

As with other lattice-based KEMs, decapsulation failures may potentially leak information about the private key. However, NTRU+ is designed so that the probability of a decapsulation failure for an honestly generated ciphertext is negligible, rendering such failures irrelevant in practice.


The IND-CCA2 security of NTRU+ is based on the hardness of the NTRU and Ring-LWE problems and, in particular, admits a tight security reduction in the random oracle model. The security proof begins with the construction of an underlying NTRU encryption scheme (denoted as GenNTRU in {{KP26}}), which is OW-CPA secure under the NTRU and Ring-LWE assumptions.

The explicit rejection of invalid ciphertexts follows from the 𝛾-spreadness property of GenNTRU, while the re-encryption-free FO transform relies on the rigidity properties of both GenNTRU and SOTP.


## Rejection Sampling in Key Generation

The key generation process employs rejection sampling. Specifically, fresh 32-byte random seeds SHOULD be obtained from an approved RBG until both polynomials f and g are invertible in the ring R.

The probability that a randomly generated small polynomial is invertible in R can be heuristically estimated from the CRT-based ring decomposition. For NTRU+768 and NTRU+864, the decomposition reaches degree-2 and degree-3 factors, respectively, giving approximate per-polynomial invertibility probabilities of (1 - 3457^{-2})^{768/2} ≈ 1.00 and (1 - 3457^{-3})^{864/3} ≈ 1.00. For NTRU+1152, the corresponding probability is approximately 0.71. Consequently, the probability that both f and g are invertible is approximately 1.00 for NTRU+768 and NTRU+864, and approximately 0.51 for NTRU+1152.

As shown in Table 2, key generation in NTRU+ remains computationally efficient in both the optimized C and AVX2 implementations. Even for NTRU+1152, where rejection sampling is more frequent, the expected number of attempts is less than two, and the resulting overhead is negligible in practice.

The use of rejection sampling implies that the execution time of key generation is not strictly constant and exhibits some variance. However, this timing variation depends only on the randomness used during key generation and does not affect the security of the generated key pair.

Polynomial inversion in NTT representation can be efficiently implemented using the hierarchical batch inversion technique of {{KCP26}}, which applies Montgomery's trick to reduce the number of field inversions.


## Pairwise Consistency Testing Considerations

The NTRU+ key-generation procedure described above does not include a Pairwise Consistency Test (PCT). Implementations seeking FIPS 140-3 validation MAY perform a PCT following CMVP guidance by executing an encapsulation and a subsequent decapsulation using a newly generated key pair and verifying that both operations derive the same shared secret. While such a test can reliably detect non-functional key pairs, it provides only limited assurance against malformed or fault-induced keys that continue to operate correctly on honestly generated ciphertexts.

We note that NTRU+ may admit a more direct form of Pairwise Consistency Test (PCT) than the encapsulation-decapsulation test described above. In particular, it may be possible to verify certain mathematical consistency relations between the public key pk = h and the private key sk = (f, h^{-1}, F(pk)) by exploiting properties of the SOTP encoding and the CBD-based construction used in NTRU+. Exploring such an approach is beyond the scope of this document. Moreover, incorporating such a PCT would likely require modest modifications to the current key-generation algorithm.


## Input Entropy of Keying Material

The encapsulation algorithm internally samples an n-bit random message, from which a 32-byte shared secret is derived. This contrasts with ML-KEM, where the encapsulation process always starts from a fixed 32-byte random value. As a result, NTRU+ uses a larger amount of internal encapsulation randomness.

While a 32-byte randomness source is sufficient for currently targeted security levels, the NTRU+ design retains the flexibility to accommodate larger amounts of input entropy should future cryptographic requirements evolve.

## Input Validation Checks in Encapsulation and Decapsulation

During encapsulation, an implementation SHOULD perform a public-key type check on the public key pk before processing. If this validation fails, encapsulation MUST NOT continue. NTRU+ does not require an explicit modulus check on each coefficient of pk during encapsulation. Any modification of the public key results in a mismatch between the value F(pk) stored in the private key and the hash value derived from the modified public key during encapsulation, causing the decapsulation procedure to reject the resulting ciphertext.

During decapsulation, an implementation SHOULD perform both a ciphertext type check and an explicit modulus check on the received ciphertext. If either validation fails, decapsulation MUST NOT continue. In particular, the explicit modulus check is essential because the decapsulation procedure of NTRU+ does not perform a re-encryption and ciphertext equality check as part of the FO transform.

The modulus check is particularly important in NTRU+, where the modulus is q = 3457 and each ciphertext coefficient is represented as a 12-bit integer. The gap between the modulus q and the 12-bit representation permits multiple encodings of the same element in the ring R. Without an explicit modulus check, this ambiguity can potentially lead to IND-CCA2 attacks.

For example, if a ciphertext coefficient is equal to 1, an adversary may replace it with 3458. Both values can be represented as valid 12-bit integers and satisfy 3458 ≡ 1 (mod 3457). As a result, an adversary can construct a ciphertext that is distinct from a target ciphertext at the bit-string level while remaining equivalent modulo q, thereby violating the uniqueness of ciphertext encodings assumed by the security proof.

In addition, an implementation SHOULD verify that the private key has the expected length and corresponds to the intended parameter set before performing decapsulation. If this validation fails, decapsulation MUST NOT continue. Unlike the ciphertext modulus check described above, this private-key validation is primarily intended to detect malformed inputs and implementation errors rather than to enforce a security property of the NTRU+ construction itself.

## Explicit Rejection in Decapsulation

Unlike ML-KEM, which returns a pseudorandom shared secret for an invalid ciphertext, NTRU+ provides explicit rejection and returns a decapsulation error. Protocol designers should understand both the benefits and limitations of this design choice.

In many authenticated key-exchange protocols, explicit rejection is not strictly necessary. Even if a KEM returns a pseudorandom shared secret for an invalid ciphertext, the resulting key material is typically used to derive symmetric encryption keys and MAC keys. Subsequent protocol steps, such as MAC verification or key confirmation, will fail if the two parties derive different shared secrets. As a result, protocols such as TLS, SSH, and IKE can safely operate with either implicit rejection or explicit rejection.

However, explicit rejection becomes more useful when a KEM is employed in a public-key encryption setting. In such applications, the derived shared secret is often used directly to decrypt a protected payload. If implicit rejection is used, the recipient must first determine whether the derived key is valid before attempting decryption. This typically requires an additional integrity check, such as an authenticated-encryption tag or a MAC. By contrast, explicit rejection allows the recipient to terminate processing immediately upon detection of an invalid ciphertext.

A similar consideration arises when a KEM is used as an authentication mechanism. In a typical KEM-based authentication protocol, the verifier sends a ciphertext and the prover responds with an additional protocol message, such as a MAC, to demonstrate possession of the correct shared secret. When implicit rejection is used, decapsulation always produces a candidate shared secret, and therefore the verifier must rely on the subsequent protocol message to determine whether decapsulation succeeded. In contrast, with explicit rejection, invalid ciphertexts are detected and rejected during decapsulation itself, allowing the protocol to terminate immediately upon failure and simplifying the protocol logic.

Therefore, explicit rejection does not generally provide a significant advantage in authenticated key-exchange protocols that already incorporate key confirmation, authenticated encryption, or equivalent integrity checks. Nevertheless, it can simplify protocol design in public-key encryption and KEM-based authentication settings by allowing invalid ciphertexts to be detected directly during decapsulation, rather than requiring validation through subsequent use of the derived key.


## Side-Channel Leakage in Hash Computation

The following discussion is intended to illustrate a potential attack strategy and does not constitute a complete attack analysis. During decapsulation, a ciphertext c = hr + m is first processed to recover candidate polynomials r and m. The decapsulation algorithm then computes G(r) using a hash function G, and the pair (G(r), m) is used as input to the SOTP decoding procedure. Implementations SHOULD ensure that the computation of G(r) does not leak side-channel information about its input. Otherwise, information about the underlying message polynomial m may be leaked. This observation is similar in spirit to the side-channel attack of {{UXT21}}, which exploits leakage during the hash computation performed as part of KEM decapsulation.


One possible attack proceeds as follows. Let c be a target ciphertext and let c_i denote one of its coefficients. The adversary guesses that the corresponding message coefficient m_i is zero and constructs a modified ciphertext c' by adding 1 to c_i. The modified ciphertext is then submitted to a decapsulation device holding the unknown private key sk, while the adversary observes side-channel information associated with the computation of G(r).

If the adversary can determine that the same polynomial r is hashed during the decapsulation of both c and c', then information about m_i is obtained. In particular, when m_i=0, the modification causes the recovered message polynomial to change from m to m', while the relation c' - m' = c - m continues to hold with high probability. Consequently, (c'-m')h^{-1} = r, and the same polynomial r is supplied to the hash function. Detecting this event through side-channel observations allows the adversary to distinguish whether m_i=0, thereby revealing information about the decrypted message.

Implementations SHOULD ensure that the computation of G(r) is performed in constant time and does not reveal information about its input through timing, power consumption, cache access, or other side channels.

One important distinction between the side-channel attack of {{UXT21}} and the attack described above is that the former may lead to recovery of private-key information, whereas the latter is limited to leakage of information about the decrypted message. As a result, the latter does not directly threaten the long-term secrecy of the private key. Nevertheless, both attacks demonstrate the need for side-channel-resistant implementations of the hash computation performed during decapsulation.


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

<!-- TODO acknowledge. -->
