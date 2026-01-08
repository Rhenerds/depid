# DEPID (Detached Embedded-Proof IDentifiers)

DEPID is a 128-bit identifier specification designed for decentralized applications. It enables **mathematically guaranteed instance-to-instance uniqueness** and **embedded source attribution** without the need for a central registry.

## Key Features

* **Decentralized Attribution:** Identify the origin (issuer) of an ID in O(1) time.


* **Algebraic Uniqueness:** Uses prime-field divisibility to ensure global uniqueness with minimal shared knowledge.


* **Collision Resistant:** Combines high-entropy "Naked IDs" ($Q$) with algebraic alignment to prevent overlaps.


* **Opaque & Secure:** Employs Keyed-Folding to protect against GCD and prime-recovery attacks.


* **Time-Sortable (v3):** Includes an embedded Unix timestamp for native lexicographical ordering.

---

## How It Works: The Algebraic Basis

The core of DEPID is mapping a high-entropy "Naked ID" ($Q$) into a verifiable prime field based on a source-specific 20-bit prime ($P$).

### 1. Prime-Field Alignment

A 100-bit (v2) or 64-bit (v3) entropy source is shifted and aligned to the nearest multiple of the issuer's assigned prime :

$D=(Q≪20)−((Q≪20)\mod P)$

This ensures , making the ID mathematically verifiable.

### 2. Masking (Folding)

To prevent unauthorized parties from identifying the prime, the value is obfuscated using a deterministic XOR mask:

$X=D⊕fold{_L}​(P)$

### 3. O(1) Verification

Receivers extract an 8-bit **Scattered Hint** ($H$) to perform subset selection. Instead of checking all primes, they only test the small "bucket" of candidate primes associated with that hint, resulting in constant-time identification.

---

## Specification & Formats

### Binary Layout (128-bit)

| Segment      | Bits | Description                   |
| ------------ | ---- | ----------------------------- |
| **Hint (H)** | 0–7  | 8-bit Keyed Hint for routing. |
| **Version (V)** | 8–11 | 4-bit Version identifier. |
| **Payload (K)** | 12–127 | Algebraic/Entropy field (116 bits). |

### Versioning

* **v1 (Algebraic):** Unkeyed fixed sets.
* **v2 (Hardened):** Two-keyed masking for max entropy ().
* **v3 (Temporal):** Includes 32-bit Unix timestamp for chronological sorting.

### String Representation

DEPID uses a **2-1-29** (v2) or **2-1-8-21** (v3) hex-and-dash format:

* **v1:** `HH-1-KKKKKKKKKKKKKKKKKKKKKKKKKKKKK`
* **v2:** `HH-2-KKKKKKKKKKKKKKKKKKKKKKKKKKKKK`
* **v3:** `HH-3-TTTTTTTT-KKKKKKKKKKKKKKKKKKKKK`

---

## Anti-Collision Fail-safes

To guarantee 100% uniqueness, the generation process utilizes three layers of protection:

1. **Progressing Entropy:** Use of a CSPRNG and/or progressing counters/timestamps.
2. **Cross-Prime Check:** A "Bucket Collision" rule ensures the generated ID isn't accidentally divisible by other primes in the same hint bucket.
3. **Local DB Check:** A final fail-safe check against the local issuance database.

---

## License

Copyright (c) 2026 Rhendra Networking. Distributed under the **MIT License**.