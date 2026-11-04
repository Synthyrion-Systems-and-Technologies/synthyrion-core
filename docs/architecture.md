> Snapshot of the matching page on https://synthyrion.com (site build 20261104_release_011, release 0.1.1). The website is the current version; this copy is refreshed with each release.

# Architecture 

What the kernel is made of and how programs use it. The call-by-call reference is generated on the [API page]; what exists and how strongly it is checked is on the [features] and [proofs] pages. 

**Status.** Synthyrion Core L5 is a capability-based microkernel written in Rust. Its primary target is x86-64. AArch32, AArch64 and RISC-V 64 ports run in emulation (QEMU) only. It is under active development. Its feature status is published on this site, with the strength of each piece of evidence (tested / bounded / panic-free / functionally proven; host, virtual machine or hardware). **It has no safety, security or government certification. There is no whole-kernel functional-correctness proof. It is not FIPS 140-3 validated.** 

## §1Domains: what a program is 

Only the kernel runs in the CPU’s most privileged mode. Everything else is a **domain**: a driver, a file-system service, a network service or an application, running in ring 3 with no port I/O and no privileged instruction. A domain owns a private address space. Its page tables map only its own image and the pages it was granted; every other page of the machine is supervisor-only, so a stray access is a CPU page fault that stops that domain and nothing else.  
- **Write XOR execute.** Code pages are read and execute; data, bss and stack are read and write and non-executable. No page is both. 
- **Faults are contained.** A fault in a domain stops that domain; the kernel records the fault and keeps running. 
- **Hangs are contained.** A domain that runs more than a few timer ticks without yielding is stopped. 
- **Restart under a budget.** A stopped domain may be restarted from a fresh image a limited number of times; at zero it stays dead and its grants are revoked.  

## §2Two gates 

A domain calls the kernel through software interrupts. The **domain gate** (`int 0x81`) carries the calls driver domains need: yield, exit, log, PCI configuration access limited to safe fields, IPC, and the grants the host gave. The **kernel gate** is the capability gate for applications and services: the caller names a capability by its address in its own capability table, with the number of address bits to resolve. A refused call returns a status word and changes nothing. Both gates, every selector, argument and status word are listed on the [API page], generated from the single ABI definition. 

## §3Capabilities 

Authority is a **capability**: an object, a set of rights and a badge, held in a slot of the holder’s own table (its CSpace). The kernel mints capabilities and seals them; a call is checked against the presented capability (genuine, not revoked, right kind of object, enough rights) before anything happens, and a refusal leaves the system unchanged. A capability can be copied, minted with fewer rights, moved and revoked through a derivation tree. Kernel objects are carved from untyped memory by retyping, so the kernel does not grow memory on its own. The table on the [features page] states, row by row, how much of this model exists. 

## §4Messages and notifications  
- **Call and reply.** A caller sends through an endpoint capability and blocks until the callee answers with the one-shot reply capability it was handed. Capabilities can travel in a message only if the endpoint capability carries the grant right. 
- **Send and receive.** A plain send never blocks. A receiver reads its own endpoint. 
- **Notifications.** A notification object carries a word of signal bits without a message, for interrupts and events. 
- **Faults.** A fault is delivered as a message to the task’s fault handler.  

## §5Tasks and scheduling 

Task operations (suspend, resume, read and write saved registers, configure priority, controlled priority limit, fault handler) each take a task capability with the needed right; none takes a bare task number as authority. Scheduling supports fixed partition windows so that one partition’s timing does not depend on another’s. Non-interference is checked for the scheduler only, over a bounded domain, and only while a partition schedule is installed: see the [proofs page]. 

## §6Boot 

The x86-64 image boots through UEFI. The boot information handed to the kernel is a defined structure, and the kernel’s identity is a signed value checked at boot (ML-DSA-87, FIPS 204). The primary target is x86-64; the AArch64, AArch32 and RISC-V 64 ports run in emulation (QEMU) only: see [platforms]. 

## §7What this page does not claim 

This is a description of the design. That a piece exists, is wired to a gate, or is checked is stated per row on the features page, with the strength of the evidence. Pieces that are described in the project’s notes but are not yet wired are marked “In progress” or “Planned” there.
