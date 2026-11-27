# Security model and cryptography

This chapter states what the kernel protects, how, what it assumes, and which cryptographic primitives it uses. It states plainly what is not claimed.

## 1. What the kernel enforces

| property | mechanism | strength |
|---|---|---|
| no authority without a capability | every call that acts on an object presents a capability for it; there are no ambient-authority calls | tested; bounded checks of the capability model against a reference model |
| capabilities cannot be forged | the kernel seals each capability with a keyed authentication code only it can compute; programs hold slot addresses, not capability bytes, and the kernel verifies in constant time | tested |
| rights only narrow | copy, mint and mutate take a mask that can only clear rights; a mask that sets a bit no right claims is refused | tested and bounded |
| revocation is real | revoking a capability removes all its descendants through the derivation tree; revocation is budgeted so it can be preempted | bounded |
| refusals change nothing | every call checks everything before acting | tested |
| write xor execute | a page is never writable and executable; code needs a frozen frame plus a separate authority | tested; bounded |
| memory is never allocated for you | objects are carved from untyped memory the caller owns; reused memory is cleared | bounded for the clearing property |
| fixed capacities | every kernel table has a named capacity and a counted refusal; no silent growth, no panic | tested; a timing-analysis input |
| a device cannot reach what it was not given | IO spaces confine DMA; a device in no space is denied, never passed through | host and emulation; see platforms for exactly where |
| faults are contained | a fault stops one task or domain; the kernel keeps running | tested |
| time isolation | optional fixed partition windows; strict priorities with a bound on delegated priority | non-interference of the scheduler checked over a bounded domain only |

Strength words are the four used on the Proofs page: tested, bounded, panic-free and functionally proven. Nothing here is functionally proven.

## 2. What is assumed (the trusted base)

* The hardware, including the CPU's memory protection, the IOMMU when it is relied on, and firmware.
* The Rust compiler and code generator. There is no binary-level validation yet.
* The kernel's checking tools are first-party and are not themselves verified.
* The kernel's own code: it is small compared with the system on top of it, but it is not proven correct.
* The root task, which receives every initial capability. It is as trusted as the system builder makes it.

## 3. Cryptography

### What the signed 0.1.3 image links

The image links one cryptographic crate, `synthyrion_kcrypto`: SHA-512 (the crate also carries the 384-bit variant of SHA-2) and HMAC-SHA-512. The kernel uses them to seal capabilities (HMAC-SHA-512, the tag truncated to 32 bytes) and to compare tags in constant time. The image contains **no cipher** and **verifies no signature at boot**: the dependency tree of its loader has no signature crate (checked with `cargo tree` on the source in the package, 2026-11-10).

### What the source export also carries, not linked into the signed image

| component | what it is | symmetric key | notes |
|---|---|---|---|
| `synthyrion_mldsa` | ML-DSA-87 (FIPS 204) | none | used by `tools/synthyrion_sigcheck`, the signature checker |
| `synthyrion_crypto` | SHA-2, SHA-3/SHAKE, HMAC; ChaCha20 (RFC 8439) with an HMAC-SHA-512 encrypt-then-MAC AEAD; a classical Ed25519 module | ChaCha20: 256 bits | nothing outside tests calls the Ed25519 module; the key vault's approvals moved to ML-DSA-87 |
| `keyvault` | the key vault: HMAC-SHA-512 tags, ML-DSA-87 signing seeds, SEAL512 sealing (SHAKE256 keystream, HMAC-SHA-512 tag) | 512 bits | read-only opener for old 256-bit blobs; as of 2026-11-27 |
| `measured_boot` | seals a secret to a boot measurement with SEAL512 (format 2); format-1 blobs (ChaCha20, 256-bit key) can only be opened read-only | 512 bits | as of 2026-11-27 |

### The floor, and where this does not meet it

The project's rule is: symmetric keys of at least 512 bits, post-quantum signatures and key establishment, hashes of SHA-512 or stronger, and no classical-plus-post-quantum hybrids in its own protocols. ChaCha20 takes a 256-bit key and is below the symmetric floor. The key vault and measured boot were migrated to SEAL512 (512-bit keys) and now only open old blobs read-only. The crypto-floor check run on 2026-11-27 (source commit 0368426a0) still lists 34 sub-512-bit symmetric sites in 5 files: the TLS 1.3, TLS-session and Tor onion code (the peer fixes a 256-bit cipher), the legacy sealing primitive kept for those users, and the conformance-test runner. The web store (separate from the kernel package) seals its own files with a first-party construction under 512-bit keys: a SHAKE256 keystream and an HMAC-SHA-512 tag; it is not a standardised mode and has had no independent review. SEAL512 is the project's own construction from standard primitives; no standard cipher has a 512-bit key, and SEAL512 has had no independent review. The ML-KEM-1024 (FIPS 203) key encapsulation exists in the development tree and is not part of this package; the kernel performs no key establishment.

## 4. Side channels and time protection

* **Partition windows** remove scheduler timing dependence between partitions, within the checked bounds above.
* **Time protection** (flushing and padding on a domain switch) has been measured on one test CPU: the L1 data cache, the L2 cache and the switch-duration channels are closed by the shipped mechanism; flushing is required, because padding alone leaves a residual correlated with the secret. **The last-level cache channel is still open on that CPU**: page colouring removed about 87 percent of the gap, not all of it, and the remaining cause is not established. An opt-in write-back-and-invalidate on every switch hides it at 13 to 19 times the cost of a switch. This is one CPU and one kind of run; other CPUs are not measured. Cache allocation technology is absent on that part.
* **Constant-time** comparison is used for every tag the kernel compares.
* **Spectre-class** speculative-execution attacks are not claimed to be excluded. Predictor flushing beyond the barrier already used on a switch is planned work, and some prefetcher and shared-buffer effects have no software mitigation on current hardware.

## 5. Reporting a vulnerability

Read the security disclosure policy on the legal pages first, then use the security address given on the Contact page. The software carries no support or service-level commitment.

## 6. What is not claimed

No certification of any kind (no common-criteria evaluation, no safety certification, no government approval). No whole-kernel functional-correctness proof. No confidentiality proof for IPC, memory or capabilities (non-interference is checked for the scheduler only). No claim that emulation results hold on hardware.
