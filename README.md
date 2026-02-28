# xdv-crypto

XDV Cryptographic Architecture - Classical, Post-Quantum, and Cross-Domain Cryptography.

**Specification:** XDV-031  
**Status:** Normative (Draft)  
**Depends On:** XDV-001 through XDV-030

---

## Purpose

`xdv-crypto` implements the Cryptographic Architecture of XDV, providing:

- Classical cryptography (SHA-256/384/512, ECDSA, RSA, AES-256-GCM, HMAC)
- Post-quantum cryptography (ML-DSA, SLH-DSA, ML-KEM, Kyber)
- Hybrid key derivation combining classical and PQ cryptography
- Cross-domain authentication for K/Q/Φ domains
- Capability token security
- Hardware attestation interface

Cryptography in XDV protects capability tokens, domain authentication, resource contracts, orchestration logs, and Virtual Hybrid Machine (VHM) isolation.

---

## Architectural Principles

Cryptographic operations in XDV SHALL satisfy:

1. **Deterministic verification behavior** - identical inputs produce identical outputs
2. **Post-quantum resilience** - resistant to quantum adversaries
3. **Capability confinement** - keys isolated per process/VHM
4. **Replay compatibility** - verification logic reproducible under replay
5. **Domain separation** - cryptographic isolation across domains

---

## Project Structure

```
xdv-crypto/
├── LICENSE                    # Dust Open Source License (DOSL)
├── State.toml                # Workspace manifest
├── README.md                 # This file
├── src/
│   ├── crypto_bridge.ds      # Main entry point
│   ├── classical/
│   │   ├── crypto_classical.ds
│   │   ├── hash.ds           # SHA-256/384/512
│   │   ├── signature.ds      # ECDSA, RSA
│   │   ├── symmetric.ds      # AES-256-GCM
│   │   └── mac.ds            # HMAC
│   ├── postquantum/
│   │   ├── crypto_postquantum.ds
│   │   ├── signature_pq.ds   # ML-DSA, SLH-DSA
│   │   ├── kem_pq.ds         # ML-KEM, Kyber
│   │   └── hash_pq.ds        # PQ-resistant hashing
│   ├── hybrid/
│   │   ├── crypto_hybrid.ds
│   │   └── key_derivation.ds # KDF(K_classical || K_postquantum)
│   ├── capability/
│   │   └── crypto_capability.ds  # Token signing/verification
│   └── attestation/
│       └── hw_attestation.ds # Hardware provider authentication
└── docs/
    └── cryptographic_architecture.md
```

---

## Classical Cryptography

### Supported Primitives

| Category | Algorithms |
|----------|------------|
| Hashing | SHA-256, SHA-384, SHA-512 |
| Signatures | ECDSA (P-256, P-384), RSA-2048/4096 |
| Symmetric | AES-256-GCM |
| MAC | HMAC-SHA256, HMAC-SHA384 |

### Deterministic Verification

Signature verification and hashing MUST produce identical results for identical inputs. Cryptographic randomness SHALL NOT influence:
- Authorization decisions
- Scheduler behavior
- Event ordering

---

## Post-Quantum Cryptography

### Supported Primitives

| Category | Algorithms |
|----------|------------|
| Signatures | ML-DSA-44/65/87, SLH-DSA-SHA2/SHAKE |
| KEM | ML-KEM-512/768/1024, Kyber-512/768/1024 |
| Hashing | SHAKE-256, SHA-3 |

### Forward Security

Compromise of classical keys SHALL NOT compromise PQ-protected assets. PQ adoption SHALL NOT weaken deterministic orchestration.

---

## Hybrid Key Derivation

Hybrid key derivation combines classical and PQ cryptography:

```
K_H = KDF( K_classical || K_postquantum )
```

For identical inputs, random seeds, and negotiation parameters, derived keys SHALL be identical.

---

## Cross-Domain Authentication

### Authentication Flow

For domain binding:
1. Present cryptographic identity
2. Provide hardware/firmware attestation
3. Validate signature chain
4. Confirm capability scope
5. Log authentication event

### Authentication Tokens

Tokens include:
- Domain identity
- Cryptographic signature (PQ)
- Version metadata
- Hardware attestation proof
- Logical timestamp

---

## Capability Token Security

Capability tokens SHALL:
- Be cryptographically signed
- Include domain identifiers
- Include scope restrictions
- Include expiration
- Include logical timestamp

Forgery SHALL be cryptographically infeasible.

---

## Hardware Provider Authentication

Q and Φ hardware providers SHALL:
- Present signed attestation (PQ signature)
- Declare supported operations
- Declare resource limits
- Support PQ signature verification

Hardware SHALL NOT gain access without successful authentication.

---

## Replay Compatibility

Cryptographic verification SHALL be replay-compatible:
- Signature validation outcomes reproducible
- Authentication decisions reproducible
- Key derivation steps reproducible (deterministic inputs)
- Capability validation results reproducible

Randomness used in cryptographic generation SHALL NOT affect replay of verification logic.

---

## Build

```bash
cd xdv-crypto/src
dust build
```

---

## Compliance

An implementation is compliant if:
1. Classical crypto is modern and secure
2. Post-quantum primitives are supported
3. Hybrid key derivation is deterministic
4. Cross-domain authentication is mandatory
5. Cryptographic verification is replay-safe
6. Capability tokens are cryptographically protected

---

## Related Specifications

- XDV-001 through XDV-030 (prerequisites)
- XDV-034 (Audit & Attestation - uses xdv-crypto)
- XDV-040 (Cross-Domain Network Stack - uses xdv-crypto)
- XDV-060 (Q Hardware Provider Interface)
- XDV-061 (Φ Hardware Provider Interface)
