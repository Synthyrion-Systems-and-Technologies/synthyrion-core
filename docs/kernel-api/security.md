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

## 3. Cryptography in the kernel

The kernel uses cryptography for **authentication and integrity only**. It contains no cipher, no authenticated encryption and no key agreement, and a test fails the build if a confidentiality primitive enters the kernel's dependency closure.

| use | primitive | notes |
|---|---|---|
| capability sealing | HMAC-SHA-512 (RFC 2104, RFC 4231), the tag truncated to 32 bytes | verified in constant time over fixed-size tags |
| digests | SHA-512 (FIPS 180-4) | |
| image identity | ML-DSA-87 (FIPS 204) signature, checked at boot | post-quantum signature |
| key vault operations | the vault authenticates a 40-byte request binding the caller's identity and four words | the key never leaves the kernel; `vault_op` returns only a count |

### Post-quantum position

The project's cryptographic rules: key-establishment and signatures are post-quantum only, with no classical-plus-post-quantum hybrids in the project's own protocols. In the code:

* **Signatures:** ML-DSA-87 (FIPS 204). A hash-based signature (SLH-DSA, FIPS 205) is also implemented in the wider system.
* **Key establishment:** ML-KEM-1024 (FIPS 203), in the wider system. The kernel itself performs no key establishment.
* **Hashes:** SHA-512 and SHA3-512. A 256-bit hash output is below the project's post-quantum floor, which is why the project uses 512-bit outputs.

### What is not post-quantum, or not yet at the floor

Honesty requires this list:

* The capability seal is an HMAC-SHA-512 **truncated to 32 bytes**: a 256-bit tag. That is a forgery-resistance margin chosen for the seal's role, not the project's 512-bit floor for keys and hashes, and the kernel-side seal key is in kernel memory.
* Some symmetric components elsewhere in the system (for example the authenticated encryption of the key-vault's files) use 256-bit-key ciphers that are below the project's 512-bit symmetric floor and are scheduled to migrate. They are not in the kernel.
* The wider system may use conventional TLS to read public web pages, because most of the internet offers nothing else; what it receives is treated as data to be made post-quantum locally. No login, personal data or own-protocol traffic uses it. None of that code is in the kernel.
* **Nothing in this documentation is a FIPS 140-3 validation claim.** The software is not FIPS 140-3 validated.

## 4. Side channels and time protection

* **Partition windows** remove scheduler timing dependence between partitions, within the checked bounds above.
* **Time protection** (flushing and padding on a domain switch) has been measured on one test CPU: the L1 data cache, the L2 cache and the switch-duration channels are closed by the shipped mechanism; flushing is required, because padding alone leaves a residual correlated with the secret. **The last-level cache channel is still open on that CPU**: page colouring removed about 87 percent of the gap, not all of it, and the remaining cause is not established. An opt-in write-back-and-invalidate on every switch hides it at 13 to 19 times the cost of a switch. This is one CPU and one kind of run; other CPUs are not measured. Cache allocation technology is absent on that part.
* **Constant-time** comparison is used for every tag the kernel compares.
* **Spectre-class** speculative-execution attacks are not claimed to be excluded. Predictor flushing beyond the barrier already used on a switch is planned work, and some prefetcher and shared-buffer effects have no software mitigation on current hardware.

## 5. Reporting a vulnerability

Read the security disclosure policy on the legal pages first, then use the security address given on the Contact page. The software carries no support or service-level commitment.

## 6. What is not claimed

No certification of any kind (no common-criteria evaluation, no safety certification, no government approval). No whole-kernel functional-correctness proof. No confidentiality proof for IPC, memory or capabilities (non-interference is checked for the scheduler only). No claim that emulation results hold on hardware.
