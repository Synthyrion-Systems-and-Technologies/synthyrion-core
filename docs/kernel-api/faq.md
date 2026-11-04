# Frequently asked questions

Answers for users, integrators, security reviewers and procurement or legal readers. Where an answer depends on the status of a feature, it says so, and the Features, Proofs and Platforms pages carry the live detail. Nothing here is legal advice.

## A. The basics

**What is Synthyrion Core L5?**
A capability-based microkernel written in Rust. It holds capabilities, passes messages, schedules tasks, manages address spaces and object memory, delivers interrupts and faults, and provides the privileged part of virtualization. Drivers, file systems, network stacks and applications run on top of it.

**What does "L5" mean?**
L5 is the project's name for the next generation of capability microkernels: keep the capability model and pursue five further goals together. The What L5 means page lists them with their status. None is finished.

**Is it finished?**
No. It is under active development. The Features page gives the status of each feature (done, in progress, planned) and the Roadmap page the plan.

**Is it open source?**
No. It is source-available under the Synthyrion Core L5 licence. It is not an open-source licence and not the GNU General Public License. Read the licence page for what you may do.

**Is it certified?**
No. It has no safety, security or government certification, and is not FIPS 140-3 validated.

**Is it formally verified?**
No. There is no whole-kernel functional-correctness proof. There are tests, bounded exhaustive checks against models, panic-freedom proofs for parts of the capability code, and a specification-level non-interference proof. Each is labelled with its strength; see Proofs and the Security chapter.

**What does "bounded" mean?**
Checked exhaustively over every case up to a stated size, comparing the real code to a model. It is a proof about that finite domain only. A property true for three objects can fail at four.

**How big is it?**
At the status table's last size measurement the microkernel crate was about 6,500 lines including its inline tests, and the other core crates ranged from a few hundred to about 1,500 lines each. It carries no drivers, network stack or file system. Counts change with every release; the download has the current ones.

**Why Rust?**
Rust removes whole classes of memory-safety errors in safe code. It is not a correctness proof, and the compiler is in the trusted base.

**Does it use any third-party code?**
The kernel is first-party Rust with no third-party crates. Third-party software, where it appears at all, appears only inside separate applications, with its licence stated in the third-party notices.

## B. Calling the kernel

**How do I make a system call?**
On x86-64, with the `syscall` instruction: selector in `rax`, the capability address in `rdi`, its depth in `r9`, up to four arguments in `rsi`, `rdx`, `r10`, `r8`. The status comes back in `rax` and the value in `rdx`. See Calling the kernel.

**Why is there no `int 0x80` for the kernel gate?**
By decision: the x86-64 entry is `syscall`, and a second entry vector would be a second surface to check. The driver-domain gate does use `int 0x81`.

**What is a capability address and what is the depth?**
A capability address (`cptr`) names a slot in your own capability space by walking a tree of CNodes, consuming `depth` bits of the address. The same depth applies to every capability argument of one call.

**Where do I find a call's number?**
In the API reference, in the index of each gate. Numbers are stable: the table is append-only, a row is never renumbered or removed.

**Will the API change?**
Within major version 2, rows are only added. A change to an existing row's selector, name, argument order or meaning is a new major version. Rows marked defined have fixed numbers and names but no handler yet; superseded rows are kept forever and answer `NOT_WIRED`.

**What does `NOT_WIRED` mean?**
The kernel gate does not serve that selector: it is defined but has no handler yet, it is superseded by another row, or it names nothing. Nothing was changed. Check the status of the row in the reference.

**Is every call in the reference usable today?**
No. Wired rows are served by the gate dispatcher; defined rows are not served yet. How far each wired call has been exercised on a booted image, from user mode, is on the Features page. The reference marks the status of each call.

**Is there a C binding?**
A C header is generated from the same table (selectors, status words, a table macro and the prototype of the one invoke function a binding supplies). There is no C library beyond that. A Rust client is generated as well.

**Is there a Rust SDK?**
A generated, `no_std`, no-unsafe client crate with a typed function per call; the transport that performs the trap is supplied by the host program.

**How do I pass more than one word in a message?**
A message is one word plus up to three capabilities. For more data, share memory (a frame mapped into both tasks, or a shared window for a driver domain) and send the slot or offset in the word. A registered IPC buffer can also be set (`tcb_set_ipc_buffer`).

**How do I tell clients apart in a server?**
Mint a badged copy of the endpoint capability for each client with `cnode_mint`. The receive record carries the badge of the capability a message arrived through. The client cannot choose or change it.

**Can a server answer a client twice?**
No. The reply capability is single use and bound to the task it was issued to.

**What happens if a server dies while a client is waiting in a call?**
The client wakes with an aborted result; it does not hang.

**How do I wait for both messages and interrupts?**
Bind a notification to the server's task (`bind_notification`). Its receive then returns signals as kind 4, ahead of queued messages.

**Do I need a loop to revoke a big capability tree?**
Yes: `cnode_revoke_step` takes a budget and returns the number of descendants still alive; call it until it returns 0. This is what keeps one revoke from stalling the kernel.

**What are rings for?**
To batch calls: queue up to 32 per kernel entry. Calls that can block, yield, exit or open a ring cannot be carried by a ring.

**Can I call the kernel from C or assembly?**
Yes: any code that sets the registers as documented and executes the entry instruction. The generated clients are a convenience.

## C. Memory, objects and scheduling

**Why can't I allocate a kernel object directly?**
Because the kernel never allocates for you. You carve objects out of untyped memory you were given (`retype`), which makes memory a budget you can hand to a subsystem and take back.

**How do I give a component a memory budget?**
Retype an untyped capability of the right size and give the component the capability; revoking it destroys everything made from it and returns the memory.

**Is memory cleared when it is reused?**
Yes, checked over a bounded domain; it is not proven for all cases.

**How do I run code from memory I wrote?**
Write the frame, freeze it (`freeze_frame`), then map it executable with the map-exec authority (`map_frame`). Those two rows are defined but not served yet, so on the current gate this flow is not available; at present, code reaches user space through the loader. A page is never writable and executable at once.

**What is a scheduling context?**
The right to CPU time: a budget per period on a core. A task needs one to run when scheduling-context scheduling is in use.

**What is a passive server?**
A server with no time of its own. A call donates the client's scheduling context to it for the duration of the call, so the server costs CPU only when called and the time is charged to the client.

**How do priorities work?**
Strict priorities 0 to 255. A task can set another task's priority only up to its own maximum controlled priority.

**What are partitions for?**
Fixed cyclic time windows so that one partition's timing does not depend on another's. Non-interference has been checked for the scheduler over a bounded domain, only while a partition schedule is installed.

**Does it run on several cores?**
Yes, under one kernel lock, at 2, 4 and 8 cores in emulation, and on a test server. It is running, not proven: the verification so far is single-core.

**What are the real-time guarantees?**
Every table has a fixed capacity, so every lookup has a worst case. Timing analysis is in progress: one function is bounded in cycles under stated assumptions; the kernel entry points are pending with named reasons. No worst-case execution time is promised for them yet.

## D. Drivers, devices, DMA, interrupts

**How does a driver get an interrupt?**
Through a notification. The holder of the IRQ control issues the handler for a line, the driver binds it to a notification, waits, clears its device's own cause, then acknowledges the line. See Interrupts in the concepts chapter.

**Why does the line stay masked after an interrupt?**
So the driver cannot be re-interrupted before it has dealt with the device. The ack call unmasks it.

**How do I stop a device doing DMA to memory it should not touch?**
Put the device in an IO space (IOMMU address space) and map only the frames it may use. A device in no space is denied everything. This requires an IOMMU; with none, the status is `NO_IOMMU`.

**Why does a driver domain on the BIOS-boot image say DMA is unbounded?**
That image has no IOMMU code. The CPU side of the domain is enforced by page tables; the device side is not bounded there.

**Can a driver touch any PCI register?**
No. It can read the configuration space of functions it was granted and write only the command register and the interrupt-line byte. BARs, the option ROM and capability lists are never writable.

**Which drivers are included?**
Drivers are not part of the kernel. USB, AHCI, NVMe, an Intel network card, a file system and an ACPI soft-off domain exist as domains outside it; their protocols are not part of the stable kernel interface.

## E. Platforms

**Which hardware does it run on?**
x86-64 with UEFI is the primary target. AArch64, RISC-V 64 and AArch32 run in emulation only. See Boot and platforms for exactly what ran where.

**Has it booted on real hardware?**
On x86-64, parts of the work have run on test hardware; the Platforms page says which evidence is hardware and which is emulation. The kernel itself has not booted on an ARM, RISC-V or AArch32 board.

**Does it run on Windows or Linux?**
As a guest, under QEMU/KVM on Linux (emulation evidence), and under QEMU with the Windows Hypervisor Platform. Hyper-V and WSL images are not done.

**Can it host a Linux or Windows guest?**
The kernel provides VCPU and EPT/NPT objects. A user-level monitor outside the kernel provides device models. A Linux guest has been run to a first user-space process at emulation strength; Windows guests are planned.

**Does it support AMD-V?**
The object model supports both Intel VT-x and AMD-V (the VCPU family is a parameter), and AMD IOMMU has been exercised under emulation. AMD-V and AMD IOMMU have not been run on AMD hardware.

**What are the minimum hardware requirements?**
No minimum is published; none has been measured and stated.

**Does it support secure boot?**
The kernel's identity is a signed value (ML-DSA-87) checked at boot. Integration with UEFI Secure Boot's own signature scheme is not documented here.

## F. Security

**What can a compromised driver do?**
Only what its capabilities and grants allow: its own registers and memory, and its DMA window if the device is confined. A compromised domain faults or is stopped without taking the kernel down.

**What can a malicious task do?**
Use the capabilities it holds, and exhaust the budgets it was given. It cannot forge a capability, widen one, or allocate kernel memory beyond its untyped budget.

**Can capabilities be forged?**
Not by user code: they are sealed in the kernel and never leave it. The seal is HMAC-SHA-512 truncated to 32 bytes.

**Is the kernel constant-time?**
Tag comparison is constant-time. Timing channels below the architecture are a separately measured claim; see the Security chapter.

**Are there known open side channels?**
Yes, stated plainly: on the one test CPU measured, the last-level cache channel is open after the shipped mitigations, and an opt-in mitigation exists at a high cost. Other CPUs are not measured.

**Has it been penetration-tested or audited by a third party?**
No external audit or penetration test is claimed. It has its own planted-fault test discipline: each check is shown to fail when the defect is planted.

**How do I report a vulnerability?**
Read the security disclosure policy, then use the security contact on the Contact page.

**Does the kernel encrypt anything?**
No. It contains no cipher, no authenticated encryption and no key agreement; it authenticates (HMAC-SHA-512, ML-DSA-87 verification) and hashes (SHA-512). Confidentiality services live above the kernel.

**Is it post-quantum?**
The signatures it checks are ML-DSA-87 and its hashes are 512-bit. The kernel has no key agreement. The wider system uses ML-KEM-1024 for key establishment. Some symmetric components elsewhere are below the project's stated floor and scheduled to migrate; the Security chapter lists what is not post-quantum.

**Which standards does the cryptography follow?**
FIPS 180-4 (SHA-512), FIPS 204 (ML-DSA), FIPS 203 (ML-KEM), FIPS 205 (SLH-DSA), RFC 2104 and RFC 4231 (HMAC), NIST SP 800-185 (KMAC) where used. None of this is a validation claim.

## G. Assurance

**How is it tested?**
Unit and integration tests on a development machine, model-based bounded checks, QEMU boot scenes with named checks, and planted-fault ("plant") variants that must turn a check red. A check that has not been shown to fail is not trusted.

**How many tests pass?**
The live count is on the Assurance goals page, generated from the status table. It changes with releases.

**What does the Assurance goals page show?**
The Features page lists every feature the kernel sets out to have, row by row, with its status. The Assurance goals page lists the assurances it aims for, from a specification in a theorem prover to timing bounds, with what is achieved, what is not, and how strong the checking is. The kernel has no functional-correctness proof today; the pages show how far each goal has got.

**What would a proof of this kernel cover?**
When a functional-correctness proof exists, the Proofs page will say so with its scope. A proof about a finite model, or about the abstract specification, is not that.

## H. Operations

**How do I know what version I have?**
The ABI version is 2.0 (major, minor). A table identity (a 64-bit hash of the whole call table) lets two peers notice they were built from different tables.

**How do I verify my download?**
Compare the SHA-512 of the file with the one on the download page, and verify the ML-DSA-87 signature with a checker built from the source against the published key digest.

**Can I run it in a virtual machine?**
Yes: QEMU with OVMF. It is emulation evidence only.

**Is there support?**
There is no support obligation, service level, response time or uptime commitment. See the Contact page.

**Where is the source?**
In the download, with its tests and the checking evidence that exists today.

## I. Licensing, export and legal

These are pointers, not legal advice. Read the texts themselves and ask counsel.

**What licence applies?**
The Synthyrion Core L5 licence, version 2.1, in effect for each download from 1 November 2026 (attorney review is ongoing; a revised version applies only to downloads made after it is posted). See the Licence page.

**Is it free?**
The Community tier is free, with no account, while you and your affiliates are below the limits stated on the Get it page and in the licence. Past those limits, paid tiers apply; they are not on sale yet. The licence defines the limits and the conversion window exactly.

**Can I use it commercially?**
Read the licence page. Do not rely on this FAQ for what is permitted.

**Can I redistribute it?**
Only under the licence's terms, with every notice, fingerprint and licence file intact. See the licence.

**Why does a download carry a fingerprint?**
Each download is a zip wrapper holding the signed release zip (unchanged), its signature, SHA512SUMS, LICENSE and FINGERPRINT.txt, a random 256-bit fingerprint made for that download with the licence version, its SHA-512 and the time. The fingerprint travels beside the signed zip, not inside it, so the signed zip's published SHA-512 and signature stay valid; the published values are those of the inner signed zip. Removing or altering the fingerprint or the licence file is a breach of the licence. The Privacy page states what is recorded at download.

**Is it subject to export controls?**
It contains encryption and is subject to the U.S. Export Administration Regulations. It is not offered to countries or regions under comprehensive U.S. sanctions, to anyone on a U.S. restricted-party list, or for the end uses the export notice names. Read the Export and sanctions notice before you download.

**Who owns contributions?**
See the contributor agreements on the legal pages.

**Where do I send a licensing or commercial question?**
Use the Contact page, or write to melissabgelfman@synthyrion.com.

## J. Troubleshooting

**A call returned `LOOKUP`.**
The capability address did not resolve in your capability space, or names an empty slot. Check the address and that `r9` holds the depth.

**A call returned `DENIED`.**
The capability is the wrong kind or lacks a required right, or is not the authority the call needs. Compare with the "capability presented" line in the call's entry. `cnode_lookup` shows what a slot actually holds.

**A call returned `INVALID`.**
An argument is out of range, a pointer is outside your memory, or an object is in the wrong state (a running task for a register read, a CNode slot already used, and so on). Read the call's result text.

**A call returned `ALREADY_MAPPED`.**
The destination slot, address or device address is already occupied. Nothing was overwritten. Delete or move the old occupant, or pick another slot.

**A call returned `NOT_WIRED`.**
The selector is not served yet, or it is superseded. Check the call's status in the reference.

**My receive returns `INVALID`.**
The reply slot is occupied: answer the pending call first.

**A task disappeared.**
It faulted with no handler (it is killed), or its handler exited. Register a fault handler to see why.

**My driver domain keeps restarting.**
It is faulting or exiting non-zero. The restart budget is finite and at zero the domain is dead and its grants are revoked. Read the log lines `DOM-FAULT` and `DOM-RESTART`.

**My driver never gets its interrupt after the first.**
The line stays masked until acknowledged. Clear the device's cause, then ack (or wait again on the domain gate).

**Where do I find something not answered here?**
The call reference, then the concepts chapter, then the Contact page.
