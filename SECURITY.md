# Security policy

Synthyrion Core L5 is under active development. It has no safety, security or government certification, there is no whole-kernel functional-correctness proof, and it is not FIPS 140-3 validated. This policy does not promise that the software is secure or that any vulnerability will be found or fixed.

## Reporting a vulnerability

Email **melissabgelfman@synthyrion.com**, or use GitHub's **Report a vulnerability** button on this repository's Security tab (private vulnerability reporting). Please do not open a public issue or discussion for a vulnerability.

- Send a short first message ("I believe I have found a vulnerability in version X") without technical detail. A secure way to receive the details will be arranged. No PGP key is offered yet.
- Include the version and build, the component, the impact, the steps to reproduce, and whether and how you want to be credited.
- If you have not heard back after 10 business days, send the report again to the same address.
- No non-disclosure agreement is needed to report. There is no bug bounty.
- Do not send personal data or other people's data.

## Scope

In scope: the Synthyrion Core L5 software you obtained lawfully from Synthyrion, in its current release, running on systems you own or are authorised to test. The AArch32, AArch64 and RISC-V 64 versions are emulation-only and are reported as such.

Out of scope: Synthyrion's websites, accounts and servers (do not test them); other people's data and systems; third-party providers; modified versions Synthyrion has not released; unsupported releases; denial of service, social engineering and physical attacks.

## The authoritative policy

The full, attorney-reviewed Security Disclosure Policy (SYN-L5-DOC-08) is at https://synthyrion.com/legal/security.html. A machine-readable contact file (RFC 9116) is at https://synthyrion.com/.well-known/security.txt. If this file and the website differ, the website controls.

## Supported versions

Only the current release published at https://synthyrion.com receives fixes. Synthyrion has no support obligation and no service levels (licence section 8.4); every reasonable effort is made to fix reported bugs as soon as possible.
