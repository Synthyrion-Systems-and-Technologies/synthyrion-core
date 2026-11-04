# Overview and concepts

Synthyrion Core L5 is a capability-based microkernel written in Rust. This chapter explains the ideas you need before you read the call reference: what runs where, how authority is held and passed, how memory and time are handed out, and how messages, interrupts and virtual machines fit in. Every term used in the reference is defined here.

Status first, because it frames everything: the kernel is under active development, has no safety, security or government certification, and has no whole-kernel functional-correctness proof. What exists, and how strongly each piece is checked, is stated row by row on the Features and Proofs pages. Where this documentation says that something is partial or not yet served, it says so in plain words.

## 1. What a microkernel is, and what this one contains

Only the kernel runs in the CPU's most privileged mode. Everything else is a program on top of it: a driver, a file-system service, a network service or an application. The kernel does a small number of things: it holds the capabilities, passes messages, schedules tasks, manages address spaces and object memory, delivers interrupts and faults as messages, and provides the minimum needed to run virtual machines. Drivers, file systems, network stacks and applications are outside it.

The released kernel is the capability and object core, the physical and virtual memory managers, the scheduler, the UEFI loader and start-up, and the system-call gate. It contains no network stack, no file system and no device drivers.

## 2. Tasks, domains and programs

* A **task** is a schedulable thread of control with a saved register state, a priority, and a **capability space** (CSpace): the table of capabilities it holds.
* A **domain** is a ring-3 driver program run by the driver host: it has a private address space that maps only its own image and the pages it was granted, and talks to the kernel through the driver-domain gate. See [Driver domains](domains.md).
* An **address space** is a page-table tree. Several tasks may share one.

A fault in a domain or task stops that domain or task and nothing else. The kernel records what happened and keeps running.

## 3. Capabilities: how authority works

A **capability** is an object, a set of rights, and a badge, held in a numbered slot of the holder's own capability space. There is no other way to act on an object. No call takes a bare number as authority: every call that acts on something takes a capability for it, and the kernel checks the capability before anything happens.

* **Kinds.** The kinds are memory (value 1), endpoint (2), device (3), vault (4), task (5) and notification (6).
* **Rights.** The rights are READ (1), WRITE (2), EXECUTE (4), MAP (8), GRANT (16), SIGN (32) and DECRYPT (64). A capability can be narrowed, never widened.
* **Unforgeable.** A capability the kernel hands out is sealed with a message authentication code that only the kernel can compute, so a program cannot manufacture authority by writing the right bytes. Sealed capabilities never leave the kernel: a program names a capability by the **address of a slot** in its own capability space (see [Calling the kernel](calling.md)).
* **Badge.** A badge is a number stamped, once, on an endpoint or notification capability when it is minted. The receiver of a message learns the badge of the capability it came through. That is how a server tells its clients apart without trusting anything a client says about itself.
* **Derivation.** Copying or minting a capability makes a child of the original in a derivation tree, with rights equal to or less than the parent's. Deleting removes one capability. **Revoking** removes every descendant of a capability. Revocation is performed in budgeted steps so a very large revoke never stalls the kernel for an unbounded time.
* **Refusals change nothing.** A refused call returns a status word and leaves the system exactly as it was.

The Features page states, row by row, how much of this model exists, and where the design differs from the textbook one.

## 4. Objects and untyped memory

Kernel objects (frames, CNodes, tasks, notifications, scheduling contexts, address-space tables, VCPUs) are not allocated by the kernel on a program's behalf. They are made by **retyping**: a program holding an **untyped memory** capability asks the kernel (call `retype`) to carve objects of a given kind out of it, and the capabilities for the new objects land in slots of a CNode it names. The memory is charged to the untyped capability. Consequences:

* A program can never make the kernel run out of memory for other programs. It can only use what it was given.
* Giving a subsystem an untyped capability is giving it a memory budget. Revoking that capability destroys everything made from it and returns the memory.
* The memory of a destroyed object is cleared before it is reused (checked over a bounded domain, not proven for all cases).

A **CNode** is a table of capability slots (2 to 256 slots, a power of two). A capability space is a tree of CNodes addressed by a capability address plus a depth in bits (see [Calling the kernel](calling.md)).

## 5. Address spaces and memory mappings

Frames are objects too: a **frame** capability is the right to a piece of memory, and mapping it into an address space is an act the kernel performs only if the capability's rights allow it. The kernel enforces **write xor execute**: a page is never both writable and executable. The mechanism is a capability one: a writable mapping needs a frame that has not been **frozen**; an executable mapping needs a frozen frame (its write right is gone for good) and a separate map-exec authority. Large frames of 2 MiB and 1 GiB exist. **ASID pools** give address spaces hardware tags so a task switch need not flush the whole TLB, and the pools own disjoint tag ranges so tasks cannot interfere through the TLB. Cache maintenance calls return line counts and never reveal a physical address.

## 6. Scheduling: priorities, partitions, scheduling contexts

* **Priorities.** Strict priorities 0 to 255. Every task also has a **maximum controlled priority**: a task can set another task's priority only up to its own limit, so delegated scheduling cannot be used to raise anything above its delegator.
* **Partitions.** An optional fixed cyclic schedule of **partition windows** gives each partition a guaranteed slice of time, so one partition's timing does not depend on another's. Non-interference of the scheduler has been checked over a bounded domain and only while a partition schedule is installed; see the Proofs page.
* **Scheduling contexts.** A **scheduling context** is the right to CPU time: a budget per period, on a chosen core. A task runs on a context, and when the budget is exhausted it is throttled, or its **timeout handler** receives a fault message so a supervisor can decide. A **passive server** has no time of its own: a call **donates** the caller's context to it for the duration of the call, so the server costs CPU only when called and the time is charged to the client. A context can also be bound to a notification so a passive server woken by a signal runs on a known budget.

## 7. Messages: endpoints, call, reply, receive

* An **endpoint** is a message queue. A task holds a capability to send (WRITE) or receive (READ) on it.
* A **message** is one machine word of payload plus up to three capabilities. Capabilities travel only through an endpoint capability that holds GRANT.
* **send** never blocks. **call** sends and blocks until the receiver **replies**. When a receiver takes a call it gets a one-shot **reply capability**: it can answer that call exactly once, and a reply cannot be replayed against a later call. If the receiver dies unanswered, the caller wakes with an aborted result.
* **receive** has one entry point for everything: a plain message, a call, a signal from a bound notification, a fault. The kind is returned.
* **reply_recv** answers and waits in one step: a server's whole main loop.

## 8. Notifications

A **notification** is a word of badge bits. **signal** ORs the signal capability's badge into the word and wakes a waiter. **wait** returns the word and clears it, or blocks until there is one. Bits, not counts: three signals of the same badge are one pending bit. A task can be **bound** to one notification so one receive loop sees both messages and signals, signals first.

## 9. Interrupts

An interrupt reaches a driver as a notification. Whoever holds the **IRQ control** capability can issue the **IRQ handler** for a line (at most one live handler per line) to a driver, which binds it to a notification. After an interrupt the line stays masked until the driver **acknowledges** it, so a driver that has not yet cleared its device's own interrupt cause cannot be re-interrupted. Revoking the control entry for a line kills every capability of the old handler at once.

## 10. Faults

A fault in a task (a bad memory access, an undefined instruction, an unknown system call, a CPU exception, an exhausted budget with a timeout handler, a VM exit) suspends the task and sends a fault message to its registered handler endpoint. The handler reads it with receive (kind 7) and answers with **reply**, which resumes the task, or with **restart_task**. With no live handler the task is killed. When a handler exits, the tasks that were suspended on it are killed, because nobody remains who could restart them.

## 11. I/O ports, DMA and the IOMMU

* **I/O ports.** An x86 port range is a capability, issued by whoever holds the I/O-port control. Ports no capability may cover (PCI configuration at 0xCF8 to 0xCFF, the interrupt controllers, the timer, the keyboard controller, the CMOS clock, the first two serial ports and port 0x61) are refused at issue.
* **DMA.** A device that can do DMA can reach all memory unless confined. An **IO space** is a DMA address space of the IOMMU: you create one with the IOMMU authority, attach a device to it, and map frames into it. A device in no space is denied everything, never passed through. An unmap returns only after the IOMMU has dropped its cached translations.

## 12. Virtual machines

The kernel provides only the privileged part of virtualization: **VCPU** objects, **EPT / NPT** (guest-physical address space) objects, VM exits delivered as fault messages, and interrupt and exception injection. Device models, firmware and a guest's operating system belong to a user-level virtual machine monitor outside the kernel. A VCPU is made by `retype`, bound once and for life to a task and a guest-physical space of the same hardware family (Intel VT-x or AMD-V), and run with `vcpu_start`. Host memory can be declared protected so no guest can be mapped onto it. Guest-physical mappings obey the same write-xor-execute rule and a guest never receives more rights than the mapper held. See the Platforms chapter for how strongly virtualization has been checked: it runs under emulation and on a small bare-metal guest, and AMD-V is not yet exercised on hardware.

## 13. Submission and completion rings

A **ring** lets a program queue many ordinary kernel-gate calls in memory it owns and serve up to 32 of them with one kernel entry, each with its own completion. Calls that can block, yield, retire the caller or open a ring cannot be carried by a ring (`NOT_RINGABLE`), and I/O-port access is not ring-eligible.

## 14. The key vault

The kernel can hold keys that programs may use but never read. `vault_op` asks the vault to perform one operation for the caller (four words in, the count of operations out); the key never crosses the boundary. Details and limits are in [Security model](security.md).

## 15. Where to go next

* [Calling the kernel](calling.md): registers, capability addresses, status words.
* [Kernel gate reference](api_kernel_gate.md) and [Driver-domain gate reference](api_domain_gate.md): every call.
* [Your first program](first_program.md): a walk through building and running.
* [Boot and platforms](boot_platforms.md): what boots where, and how strongly each result is checked.
* [Security model](security.md), [Errors and faults](errors_faults.md), [Limits](limits.md), [FAQ](faq.md).
