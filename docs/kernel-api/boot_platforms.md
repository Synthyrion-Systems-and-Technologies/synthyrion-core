# Boot and platforms

This chapter explains how the kernel starts, what the first program receives, and, for every architecture, what has actually run where. A virtual machine is not hardware: every result is labelled by where it ran.

## 1. Evidence labels used in this chapter

| label | meaning |
|---|---|
| SILICON | ran on a real machine, with the privileged boot path |
| USER-SPACE ON SILICON | real hardware ran the kernel's logic as an ordinary program; this is not the privileged MMU, interrupt-controller or exception path |
| VM | ran under an emulator or hypervisor (QEMU, Windows Hypervisor Platform). Emulation evidence only |
| HOST | ran as an ordinary test on a development machine (the model and the kernel logic) |
| NO SOURCE | nothing exists yet |

And, separately, four strengths of checking, from weakest to strongest: **tested** (checked on examples), **bounded** (checked exhaustively up to a stated size, a proof about that finite domain only), **panic-free** (shown for all inputs that named code cannot panic; says nothing about correctness) and **functionally proven** (a machine-checked proof that the code does what its specification says). No part of the kernel is at the last strength today. The live counts are on the Proofs page.

## 2. Platform status at a glance

| platform | VM | silicon | open |
|---|---|---|---|
| **x86-64** (Intel and AMD), UEFI boot, the primary target | QEMU with OVMF; QEMU on the Windows Hypervisor Platform; multi-core at 2, 4 and 8 cores | hardware runs exist for parts of the work; each feature row says whether its evidence is hardware or VM only (for example: a network-card interrupt path on a test server, an IOMMU unit on a test server, a minimal virtualization guest) | virtualization on hardware beyond a minimal guest; AMD-V on hardware; AMD IOMMU on hardware |
| **AArch64** | QEMU virt, Cortex-A72, exception levels 1 and 2, booted directly and through UEFI, with multi-core start-up | USER-SPACE ON SILICON only: the kernel-logic test suites ran as ordinary programs on Cortex-A55 cores; the kernel itself does not yet boot on hardware | a board with a serial console and a platform port |
| **RISC-V 64** | QEMU only (OpenSBI, supervisor mode, directly and through UEFI), with 4-hart user tasks, a platform interrupt controller and per-task address-space identifiers | none | a board; caches are not modelled by the emulator, so cache maintenance and the loader hand-off are unproven on real hardware |
| **AArch32** | QEMU only (ARMv7-A with large physical addressing, directly and through UEFI) | none | a board |

Host and guest support: the system runs as a guest of Linux hosts under QEMU/KVM (VM strength; the image set ships as raw, qcow2, vhdx, vmdk and ova, every file signed with ML-DSA-87). Hyper-V and WSL images are not done. A Linux guest hosted by the kernel's virtualization has been run at VM strength through a first user-space init; Windows guests are planned.

## 3. How x86-64 boots

1. **Firmware.** UEFI loads the image and hands over in 64-bit long mode with paging set up. There is no 32-to-64 transition. The image is built for the x86-64 UEFI target.
2. **Identity.** The kernel's identity is a signed value (ML-DSA-87, FIPS 204) checked at boot.
3. **Memory.** The loader provides the regions of RAM that will be handed out; each becomes untyped memory.
4. **Root task.** The kernel creates exactly one task, the **root task**, and gives it every initial capability in its root CNode (section 4).
5. **Hand-off.** From here the kernel creates nothing on its own. Every later task's authority descends from a capability in the root task's CNode.

## 4. What the root task receives

The root task's root CNode:

| slot | content |
|---|---|
| 0 | null, never filled |
| 1 | its own task capability (READ, WRITE, GRANT); its maximum controlled priority is 255 |
| 2 | the root CNode capability |
| 3 | the schedule authority (READ, WRITE) |
| 4 | the spawn authority (WRITE). Once the root task exists, the ungated task-creation paths create nothing; only this authority puts a task on the run queue |
| 5 and up | one untyped-memory capability per boot region (READ, WRITE, MAP, GRANT), in ascending physical order |
| next | one device or interrupt capability per boot device |
| the rest | empty; the boot information names the first free slot |

Nothing else exists: no memory the boot carved stays with the kernel, and no capability is installed that the boot information does not name. The root task also receives the IOMMU authority as one of the boot devices; only its holder can attach an IOMMU backend or reach it.

The **boot information** is a fixed little-endian word layout: an eight-byte magic tag, a version, the root task id, the CNode size in bits, the first free slot, the untyped regions (slot, frame count, physical base) and the devices (slot, object, rights). The receiver does not trust the bytes it was given: the decoder bounds the counts before allocating, requires the byte length to equal what the counts say, and validates that slots are contiguous from 5 and unique and that regions are non-empty, ascending and disjoint.

A boot is performed once per kernel. A refused boot changes nothing and gives back every region it carved. The boot also arms domain isolation, and a start that finds it unarmed is refused.

## 5. What the root task does next

It uses `retype` to make what it needs from its untyped capabilities (CNodes, frames, tasks, notifications), builds each new task's capability space (`task_set_cspace`), copies or mints capabilities into it, places it in an address space (`tcb_bind_vspace`) and releases it (`task_admit`). See [Your first program](first_program.md).

## 6. Per-feature evidence, in short

The Features page is generated from the project's own status table and carries one row per feature with its status, strength and limits. The statements below summarise the rows that matter most to a user; where the page and this chapter differ, the page wins.

| area | status | strength and limits |
|---|---|---|
| capabilities, CNodes, derivation, revoke | done | HOST; bounded checks against a model at stated sizes (for example one CNode of four slots, depth five) |
| endpoints, call, reply, badges, notifications | done | HOST and VM (a user-mode program on a booted x86-64 image); no silicon cycle numbers |
| scheduling contexts, donation, passive servers | done | HOST only; one schedule authority with a core argument rather than one control capability per core |
| fault delivery | done | HOST for the object model; VM for a user-mode fault in the default UEFI image |
| IRQ control and handlers | in progress | objects and calls exist; full enforcement is not complete |
| I/O ports | done | HOST only |
| IOMMU and IO spaces | done, with limits | AMD IOMMU through the capability: VM; Intel VT-d through the capability: HOST with a mock, and direct VT-d on emulation and a test server's silicon; no AMD silicon |
| address spaces, ASIDs, large frames, cache maintenance | done | HOST for the object model and the gate rows; VM for the booted paths |
| VCPU and EPT/NPT | in progress | the objects and the injection calls exist; AMD-V is a second backend not yet exercised on hardware |
| SMP | in progress | running under one kernel lock at 2, 4 and 8 cores in emulation; not a proof; the verification so far is single-core |
| timing analysis (WCET) | in progress | one function bounded in cycles under stated assumptions; the kernel entry points are pending with named reasons |
| the syscall ABI | in progress | 93 wired rows of 103; a subset exercised from user mode at VM strength |
| untyped memory and retype | in progress | at the status table's last measurement the kernel still used a heap for some of its own tables; making every kernel object come from untyped memory is not finished |

## 7. What is not claimed

* There is no whole-kernel functional-correctness proof, and no certification.
* Non-interference has been checked for the scheduler only, over a bounded domain, and only while a partition schedule is installed; an unbounded proof of the abstract specification exists at specification level, not as a refinement of the code.
* The compiler and code generator are in the trusted base: there is no binary-level validation yet.
* Emulation runs are not hardware runs. Where this chapter says VM, the result is emulation evidence.
* The release image set ships as raw, qcow2, vhdx, vmdk and ova, every file signed with ML-DSA-87; the download opens when the release is published (see the Get it page).
