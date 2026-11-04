<p align="center"><img src="brand/company-horizontal-960.jpg" alt="Synthyrion Systems and Technologies" width="600"></p>

# Synthyrion Core L5

Synthyrion Core L5 is a capability-based microkernel written in Rust, from Synthyrion Systems and Technologies. Its primary target is x86-64 (Intel and AMD). The AArch32, AArch64 and RISC-V 64 versions run in emulation (QEMU) only.

> **Status, plainly.** Synthyrion Core L5 is under active development. It has **no safety, security or government certification**. There is **no whole-kernel functional-correctness proof**. It is **not FIPS 140-3 validated**. Every claim published about it carries the strength of its evidence: tested, bounded, panic-free or functionally proven; on a host, in a virtual machine, or on hardware.

## What this repository is

This repository holds the **licence, the documentation and the explanations**: what the kernel is, what is done and what is not, how strong the checking behind each claim is, and the programmer's reference for the kernel interface.

**It does not contain the kernel source code or any binaries.** The signed release package, which includes the clean source export, is downloaded from **https://synthyrion.com**. Downloading there records your acceptance of the licence and applies the country restrictions the licence and export law require (see "Export" below).

## Get it and verify it

1. Download the current release from https://synthyrion.com (Get it). The package contains the UEFI loader image in raw, qcow2, VHDX and VMDK/OVA forms, launchers, the clean source export, `SHA512SUMS`, a CycloneDX SBOM, and an ML-DSA-87 signature (`.sig`) for every file.
2. Check `SHA512SUMS` against the sums published next to the download.
3. Verify the signatures: each `.sig` is ML-DSA-87 (FIPS 204) over the SHA-512 of the file, context `SYNTHYRION-RELEASE-SIGN-1`. The public key is `synthyrion_release_anchor.bin` in the package. Compare its SHA-512 with the release key digest published on synthyrion.com (and in [CHANGELOG.md](CHANGELOG.md)), not only with the copy in the package. The source export contains a verifier you can build yourself (`tools/synthyrion_sigcheck`, no third-party crates).

Release key SHA-512: `8f953d3e91caf5d8e316134dfa5f1d1e7cad744a4b02afaa22e0f0f084fcdcd81e445c646982f1f6fa9d1cad635f9c5a67c2187fde66129122c1a9ee0f1ec5b1`

## What the current release is

The 0.1.1 release is a **boot self-test image**: it boots, runs the syscall-gate checks and prints a PASS or FAIL verdict, then idles. It has no desktop, network stack, storage or drivers; those are not in this release. Its boot evidence is virtual-machine evidence (QEMU with KVM and OVMF), not hardware evidence. See [CHANGELOG.md](CHANGELOG.md) and [docs/status-features.md](docs/status-features.md).

## Documentation

Start at [docs/README.md](docs/README.md).

- [Architecture](docs/architecture.md)
- [Features, with honest status](docs/status-features.md)
- [Assurance goals and the strength of each claim](docs/status-assurance.md)
- [Platforms and evidence](docs/status-platforms.md)
- [Kernel interface reference](docs/README.md) (overview, calling the kernel, your first program, gate reference, limits, FAQ)

## Cryptography

ML-KEM-1024 (FIPS 203) key encapsulation; ML-DSA-87 (FIPS 204) signatures; SHA-512 and SHA3-512 hashing; a Synthyrion-designed authenticated cipher on the Keccak-p[1600] permutation with a 512-bit key (a non-standard cipher, not NIST-approved); ChaCha20 remains in two internal components and is scheduled for replacement. No AES, RSA, Ed25519, ECDSA or hybrid schemes. Nothing is FIPS-validated.

## Licence

The Software is licensed under the **Synthyrion Core L5 Source-Available Community and Commercial Dual-Threshold License, version 2.3** (file [LICENSE](LICENSE)). It is a source-available licence. **It is not an open-source licence and it is not the GNU General Public License.** A summary, which is not a substitute for the text:

- **Free** for personal, educational, research, evaluation and development use, and in production while your cumulative gross revenue from products using it is below US$100,000 **and** you run no more than 100 Active Devices (sections 3 and 4).
- **A paid Commercial License is required** when you reach either limit, after a 60-day conversion window (sections 4 and 5).
- You may modify it and redistribute it only under the same licence, with notices and fingerprints intact, and only to people who accept the licence (sections 3.4 to 3.8). Redistributed derivative kernel code must be shared under the same licence (section 3.5).
- **Export controls bind everyone who receives it** (section 11).
- No warranty, not certified for safety-critical use, no support obligation (section 8).

The licence applies to the Software distributed from synthyrion.com. The documentation in this repository is published by Synthyrion for reading. The licence defines the Software to include its associated technical documentation, so copying and redistributing these pages onward falls under the licence's redistribution terms (sections 2.13 and 3.4). Copyright 2026 Synthyrion Systems and Technologies. The controlling licence text is the one posted at https://synthyrion.com/legal/licence.html.

## Export

The Software contains encryption and is subject to the U.S. Export Administration Regulations (EAR). Its export classification is pending; until it is confirmed, treat it as ECCN 5D002. The licence (section 11) forbids transfer to embargoed countries and regions, to restricted parties, and for prohibited end uses. See https://synthyrion.com/legal/export.html.

## Security

Report vulnerabilities as described in [SECURITY.md](SECURITY.md).

## Contact

melissabgelfman@synthyrion.com

Synthyrion and Synthyrion Core L5 are trademarks of Synthyrion Systems and Technologies.
