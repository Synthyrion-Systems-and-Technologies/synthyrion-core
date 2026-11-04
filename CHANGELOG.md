# Changelog

Releases are published at https://synthyrion.com. Each release is signed with ML-DSA-87; the release key digest is the same for every release below.

Release key SHA-512: `8f953d3e91caf5d8e316134dfa5f1d1e7cad744a4b02afaa22e0f0f084fcdcd81e445c646982f1f6fa9d1cad635f9c5a67c2187fde66129122c1a9ee0f1ec5b1`

## 0.1.1 (2026-11-04)

The release self-test image for x86-64 UEFI, rebuilt from a newer kernel commit than 0.1.0 (feature rows moved; the per-row status and limits are in docs/status-features.md).

- Source export: 904 files. Kernel-core host tests: 1402 passed, 0 failed, 3 ignored. 0 third-party crates.
- Boot self-test: 104 kernel-gate rows with 513 individual checks, plus the boot-arm, gate, ring and boot-information scenes. Booted from the raw, qcow2, VHDX and VMDK forms under QEMU with KVM and OVMF. This is virtual-machine evidence, not hardware evidence. Three planted bad images (a corrupted loader, a stub loader, a blank disk) do not boot.
- Not run: a Hyper-V Generation 2 boot of the VHDX; importing the VMDK/OVA into VMware or VirtualBox. Treat both as untested.
- Package signature (ML-DSA-87) verified ACCEPT; a one-bit tampered copy is REFUSED.
- Package SHA-512: `506d2ceaf6efb1bdc799687f7e33e6edbab7de01f348191664565fa854a4bcfde3b9a8801b1e100b8eff6f885bbd231cf6173f696acea124a1ed6b79da8b8a16`
- Licence: version 2.3, unchanged.

## 0.1.0 (2026-11-04)

First public release: the release self-test image for x86-64 UEFI with the clean source export.

- Kernel-core host tests: 1265 passed, 0 failed, 1 ignored. 0 third-party crates.
- Boot self-test: 93 kernel-gate rows with 449 individual checks, plus the boot-arm, gate, ring and boot-information scenes (virtual-machine evidence).
- Licence: version 2.3.
- Superseded by 0.1.1.

## What is not in any release yet

No drivers, network stack, storage or desktop. The final validator run has not been done, so the kernel's validator rows are not yet sealed. See docs/status-features.md and docs/status-assurance.md for the full, current picture.
