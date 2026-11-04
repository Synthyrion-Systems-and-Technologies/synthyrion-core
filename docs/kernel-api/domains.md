# Driver domains

A **domain** is a ring-3 program that runs a driver or a service, under a host that grants it exactly the hardware and memory it needs. This chapter explains what a domain is, what it may touch, how it talks to the kernel and to other domains, and what happens when it fails. The calls themselves are in the [driver-domain gate reference](api_domain_gate.md).

Status: the driver-domain model is wired and stable on the x86-64 BIOS-boot kernel image; a host for the UEFI image exists with the differences listed in section 7. Device-side DMA confinement is hardware-enforced only where an IOMMU is programmed from the grant; see section 6.

## 1. What a domain is

| property | value |
|---|---|
| privilege | CPU ring 3, no port I/O, no privileged instruction |
| image | a flat binary linked at a fixed address, with a 56-byte header |
| memory | only the pages the kernel mapped user in the domain's own page tables: its image, granted device register pages, granted DMA pages, granted shared windows, and one read-only information page. Every other page of the machine is supervisor-only in those tables, so touching it is a CPU page fault |
| write xor execute | code pages read and execute, data and stack read and write and non-executable; never both |
| fault | a CPU fault at ring 3 stops that domain only; the kernel records the vector, the faulting address and the instruction pointer, and keeps running |
| hang | on the BIOS-boot image a domain that runs more than 4 timer ticks (about 220 ms) without yielding is stopped |
| restart | the host restarts a stopped domain from a fresh image under a restart budget; at zero the domain is dead and its grants are revoked |

The image header is little-endian 64-bit words at the link address: word 0 the magic `IOADOM01` (the bytes of the ASCII string, read as a little-endian word), 1 the entry point, 2 the end of code (page aligned), 3 the end of initialised data, 4 the end of the image, 5 the initial stack top, 6 reserved.

## 2. Grants: what the host gives a domain

| grant | meaning | enforced by |
|---|---|---|
| PCI function (bus, device, function) | configuration space of that one function | every `pci_read` and `pci_write` is checked |
| MMIO (base, length) | the pages of a device register window | the CPU: those 4 KiB pages are user pages, nothing around them is |
| port range | an I/O port range | mediated port calls; a domain has no `in` or `out` instruction |
| IRQ line | one interrupt line | `irq_poll` returns only granted lines; `notif_wait` only the bound ones |
| shared window (base, length, read-write or read-only) | ordinary RAM several domains and the kernel hold by name: a mailbox or a sector buffer. Never overlaps another grant | the CPU: user pages (without write for a read-only holder) in each holder's tables and supervisor-only in every other domain's |
| DMA window (base, length) | RAM a device may read and write | CPU side: user pages. Device side: bounded only where an IOMMU is programmed from the grant (see section 6) |

No two domains may hold overlapping grants: the host refuses with a conflict. A domain finds out what it holds with `grant_query` (kind 1 PCI function, 2 MMIO, 3 DMA, 4 IRQ, 5 shared window; field 0 = base or id, 1 = length, 2 = flags), never from constants compiled into it.

PCI writes are narrow: only the command register (the 16-bit write of bits 0, 1, 2 and 10) and the interrupt-line byte are writable. BARs, the option ROM and capability lists are never writable, so a driver can enable its device but never remap it.

## 3. Messages between domains

A message on the domain gate is exactly 16 bytes. An endpoint is a number from 0 to 31. A domain holds a send mask and a receive mask. Rings are bounded (64 messages deep between domains) and **drop and count** when full: a domain never blocks on a slow client. A restarted domain begins with empty request queues: what it had not answered is lost and its clients retry; a first start keeps what was queued for it. Bulk data (sectors, frames, file contents) does not travel in 16-byte messages: it goes through a **shared window** both parties hold, and the message names the slot.

## 4. Interrupts in a domain

`notif_wait` returns the badge word of the interrupt lines that fired since the last wait (bit n is the n-th line the domain is bound to; never 0) and parks the domain until one fires. Waiting also re-opens the lines that fired, so the driver must clear the device's own interrupt cause first. It is refused (`bad-syscall`, with the reason `notif-wait-without-binding`) if the domain holds no binding. A fault at ring 3 becomes a fault message to the kernel's supervisor task; the host restarts the domain only after the supervisor decides, and the restart budget still applies.

## 5. The information page

A read-only user page at address `0x01FFF000`, 8-byte words: word 0 = tick count (BIOS-boot image) or unused; word 1 = the time-stamp counter rate in Hz (0 on the BIOS-boot image); words 2 and 3 = the DMA bases the host allocated for the two USB controller types (0 = the driver's built-in constants). The domain runtime reads them through a helper.

## 6. DMA: what is and is not bounded

The CPU side of a domain's memory access is enforced by page tables. The **device side** is different: a device that can do DMA can reach memory unless an IOMMU confines it. On the UEFI image each controller is confined to its own DMA window by the IOMMU (Intel VT-d). The BIOS-boot image has no IOMMU code, and says so at start-up: the device side of DMA is **unbounded** there. The kernel gate offers IO-space calls (see the kernel gate reference, DMA address spaces) that confine devices by capability; they are served and tested at host level, with AMD IOMMU exercised under emulation and Intel VT-d on a test server's silicon; the driver domains themselves are not yet moved onto IO-space capabilities.

## 7. The two hosts

| | BIOS-boot image | UEFI image |
|---|---|---|
| page tables | private, rebuilt each slice | private, built from nothing once per start: RAM identity-mapped supervisor-only, plus exactly the granted pages as user pages |
| interrupts inside a domain | enabled; the slice timer stops a domain that runs 4 ticks without yielding | disabled: no preemption, a domain that never yields stops the machine until the platform watchdog resets it |
| time | tick counter on the information page | time-stamp counter rate on the information page |
| device DMA bound | none | IOMMU per controller |

Lessons from real hardware that shaped the UEFI host: the firmware's own page tables must not be walked (they can contain entries that point at non-table pages); the no-execute bit must be switched on explicitly because some firmware leaves it off, in which case a page-table entry with that bit set is a reserved-bit fault; and a USB controller's legacy hand-off is a platform act the host performs before the domain starts, because a domain may not write the capability list.

## 8. Writing a domain

A domain is a `no_std` Rust program built for the bare-metal x86-64 target with a linker script that places it at its link address. The domain runtime provides `yield_now`, `exit`, `log_bytes`, `pci_read`, `pci_write`, `ipc_send`, `ipc_recv`, `dma_check`, `irq_poll`, `ticks`, a poll-with-deadline helper, a 192 KiB size-class heap and a panic handler (a panic logs `DOM-PANIC` and exits non-zero). The kernel embeds the flat image. An echo domain is the worked example: it receives a request on one endpoint, inverts a pattern in place in a read-write shared window, and answers on another endpoint; a second, read-only window shows `grant_query` in use.

## 9. Domains shipped with the system

USB host-controller domains (OHCI, EHCI, xHCI, with HID and mass storage), AHCI and NVMe storage, an Intel 8254x network card, a file-system domain and an ACPI soft-off and reset domain. They are drivers and services, not part of the kernel. Their wire protocols are not part of the stable kernel interface.

## 10. Refusal names

See [Errors and faults](errors_faults.md) for the names a refused domain-gate call reports.
