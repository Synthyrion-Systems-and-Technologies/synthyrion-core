# Synthyrion Core L5 documentation

These are the public explanations of Synthyrion Core L5. They are copies of what https://synthyrion.com publishes. The website is the current version.

## Status and explanations

| Page | What it answers |
|---|---|
| [architecture.md](architecture.md) | What the kernel is and how it is put together |
| [status-features.md](status-features.md) | Every planned feature, where it stands, and its stated limits |
| [status-assurance.md](status-assurance.md) | The assurance goals and the strength of the checking behind each one (tested / bounded / panic-free / functionally proven) |
| [status-platforms.md](status-platforms.md) | Which platforms have what kind of evidence (host, virtual machine, hardware) |

## Kernel interface (programmer's reference)

Read in this order:

1. [overview.md](kernel-api/overview.md): Overview and concepts
2. [calling.md](kernel-api/calling.md): Calling the kernel
3. [first_program.md](kernel-api/first_program.md): Your first program
4. [boot_platforms.md](kernel-api/boot_platforms.md): Boot and platforms
5. [api_kernel_gate.md](kernel-api/api_kernel_gate.md): Kernel gate reference
6. [api_domain_gate.md](kernel-api/api_domain_gate.md): Driver-domain gate reference
7. [api_constants.md](kernel-api/api_constants.md): Status words, kinds and constants
8. [domains.md](kernel-api/domains.md): Driver domains
9. [errors_faults.md](kernel-api/errors_faults.md): Errors and faults
10. [security.md](kernel-api/security.md): Security model and cryptography
11. [limits.md](kernel-api/limits.md): Limits
12. [faq.md](kernel-api/faq.md): Frequently asked questions

## A reminder about status

Synthyrion Core L5 is under active development. It has no safety, security or government certification, there is no whole-kernel functional-correctness proof, and it is not FIPS 140-3 validated. Every claim in these pages carries the strength of its evidence; read the strength, not only the word "done".
