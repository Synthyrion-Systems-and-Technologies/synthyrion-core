# Errors and faults

Three kinds of "something went wrong" exist, and they are different things. A **refused call** is the kernel saying no to a request and changing nothing. A **fault** is a task doing something the hardware or the kernel forbids. A **boot or start-up refusal** is the kernel declining to start.

## 1. Refused kernel-gate calls

Every kernel-gate call returns a status word in `rax`. The full table with values is in [Status words](api_constants.md). How to read them, in the order the kernel checks:

1. `LOOKUP`: a capability address did not resolve in your own capability space, or names an empty slot. Check the address and the depth in `r9`.
2. `DENIED`: the capability resolved but is the wrong kind, lacks a required right, or is not a live capability of the needed object (revoked, stale, or not the authority the call requires). Nothing changed.
3. `INVALID`: the capability was fine but an argument is out of range, an object is in the wrong state, a pointer is not in your memory, or the target no longer exists. Nothing changed.
4. Specific words: `ALREADY_MAPPED` (destination occupied), `EXHAUSTED` (a table or pool is full), `WRITE_EXECUTE` (would be writable and executable, or writes a frozen frame), `BUSY` (device already attached, port range overlaps), `UNMAPPED`, `PROTECTION`, `HITS_PROTECTED`, `NO_IOMMU`, `HARDWARE`.
5. `WOULD_BLOCK`: the call would have to wait.
6. `NOT_WIRED`: the selector is not served: it is defined but has no handler yet, it is superseded, or it names no row. Nothing changed. Use the row that replaces a superseded one.
7. Ring-only words: `RING_FULL`, `CANCELED`, `NOT_RINGABLE`.

**Rule that makes debugging easy:** a refused call changes nothing, so retrying after fixing the cause is always safe.

## 2. Refused domain-gate calls

A refused domain-gate call returns `0xFFFFFFFFFFFFFFFF` and the kernel logs `DOM-REFUSED <domain> <reason>`. The reasons are stable strings:

| reason | meaning |
|---|---|
| `unknown-domain` | the caller is not a known domain |
| `bad-syscall` | the selector is not a domain-gate call, or the call is not allowed in this state |
| `pci-fn-not-granted` | the PCI function is not one this domain was granted |
| `pci-fn-owned-by-another-domain` | another domain holds it |
| `bar-write` | a write to a base address register |
| `rom-write` | a write to the option ROM register |
| `write-outside-command-register` | a configuration write outside the command register and interrupt-line byte |
| `command-bits-not-the-drivers` | a command-register bit the driver may not change |
| `bad-width` | a width other than 1, 2 or 4 |
| `unaligned` | an unaligned access |
| `out-of-range` | an offset out of range |
| `dma-outside-window` | a buffer not wholly inside a DMA window the domain holds |
| `dma-owned-by-another-domain` | another domain holds that DMA window |
| `endpoint-not-granted` | no send or receive right for that endpoint |
| `ipc-ring-full` | the message was dropped and counted |
| `pointer-outside-domain` | a pointer not wholly inside the domain's own memory |
| `shared-window-not-granted` | the window is not held by this domain |
| `shared-window-read-only` | a write to a window held read-only |

Lifecycle lines in the log: `DOM-GRANT`, `DOM-START`, `DOM-STOP`, `DOM-FAULT`, `DOM-RESTART`, `DOM-DEAD`, `DOM-REFUSED`.

## 3. Faults

A fault suspends the faulting task and, if it has a fault handler, queues a fault message there; the handler reads it with `receive` (kind 7) and answers with `reply` (resume) or `restart_task`. The fault kinds and their message words:

| fault code | kind | `addr` carries |
|---|---|---|
| 1 | page read | the faulting address |
| 2 | page write | the faulting address |
| 3 | page execute | the faulting address |
| 4 | unknown system call | the selector |
| 5 | undefined instruction | the instruction address |
| 6 | other CPU exception | the vector number |
| 7 | VM exit (a VCPU) | the exit reason; the instruction pointer is the guest's |
| 8 | timeout (budget exhausted, with a timeout handler) | the scheduling context; the instruction-pointer word carries its budget |

With no live handler a faulting task is killed. A task blocked in a call, waiting on a notification or already suspended is not "running", so a fault cannot be raised against it. When a handler exits, every task suspended on it is killed.

The reply to a fault: word 0 resumes; for a VM exit, word 0 resumes the guest and a non-zero word stops the VCPU.

## 4. Task-state errors worth knowing

* Register read and write calls are `INVALID` while the task is running.
* A resume of a task that is not suspended is `INVALID`.
* Priority above the authority's maximum controlled priority is `DENIED`.
* A receive while the reply slot is occupied is `INVALID` and consumes nothing: answer the pending call first.
* A second reply to one call is `INVALID` (nothing pending).

## 5. Boot and start-up refusals

* A boot that names too few CNode slots, no region or a zero region, the same device twice, or exhausts the allocator is refused and changes nothing: every region it carved is given back.
* A root task can be created once per kernel. Once it exists the ungated task-creation paths create nothing.
* Boot information received by the root task is length- and structure-checked before it is trusted (see [Boot and platforms](boot_platforms.md)).
* The kernel image identity is a signed value (ML-DSA-87) checked at boot.
* A start-up that finds domain isolation unarmed is refused.

## 6. Where limits show up

Every fixed table has a named capacity and a counted refusal; nothing grows silently and nothing panics. See [Limits](limits.md).
