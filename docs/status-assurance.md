> Snapshot of the matching page on https://synthyrion.com (site build 20261127_refresh_0368426a0_r3, source commit 0368426a0; figures carry the date they were measured or counted). Release 0.1.3 is withdrawn and no download is available now. The website is the current version; this copy is refreshed with each site refresh.

# Assurance goals

The assurances Synthyrion Core L5 sets out to achieve, what is achieved so far, what is not, and how strong the checking behind each one is. “Proven” is a word with a size: every claim carries one of four labels, from weakest to strongest.

**Status.** Synthyrion Core L5 is a capability-based microkernel written in Rust. Its primary target is x86-64. AArch32, AArch64 and RISC-V 64 ports run in emulation (QEMU) only. It is under active development. Its feature status is published on this site, with the strength of each piece of evidence (tested / bounded / panic-free / functionally proven; host, virtual machine or hardware). **It has no safety, security or government certification. There is no whole-kernel functional-correctness proof. It is not FIPS 140-3 validated.**

**Functional correctness has not been proven.** There is no machine-checked proof that Synthyrion Core L5’s code matches a specification. Everything below is weaker than that, and is labelled with how much weaker.

36 of 40 feature rows done counted 2026-11-19

892 kernel tests passing measured 2026-11-09

3 panic-freedom proofs measured 2026-11-09

13 bounded exhaustive checks measured 2026-11-09

0 functional-correctness proofs measured 2026-11-09

5,527 of 5,659 kernel functions proven panic-free recorded 2026-11-26, re-measured at the final run

1,285 Isabelle theorem statements counted 2026-11-27

303 Lean theorem declarations counted 2026-11-27

3,220 of 5,143 functions with a time bound measured 2026-11-26; loop bounds: 148 source-bound, 8 behavioural, 54 MIR-bound, 30 taken on trust

## Measured evidence /assurance/evidence

measured 2026-11-26 from the kernel-math census (TOTALS line)

The numbers in this chart

| Measure | Functions |
|---|---|
| functions | 5659 |
| proven panic-free | 5101 |
| proven by induction | 426 |
| blocked | 105 |

Kernel functions

measured 2026-10-19 from the Verus progress record (gold: verified; grey: errors)

The numbers in this chart

| Run and result | Count |
|---|---|
| run 1: verified | 43 |
| run 1: errors | 0 |
| run 2: verified | 50 |
| run 2: errors | 0 |
| run 3: verified | 44 |
| run 3: errors | 0 |
| run 4: verified | 93 |
| run 4: errors | 0 |
| run 5: verified | 24 |
| run 5: errors | 0 |
| run 6: verified | 20 |
| run 6: errors | 0 |

Verus runs

measured 2026-11-26 from the kernel WCET table (unit: MIR statements, not cycles). Loop bounds under these figures: 148 source-bound, 8 behavioural, 54 MIR-bound, 30 taken on trust; a bound taken on trust is not checked against the loop's code

The numbers in this chart

| Crate and result | Functions |
|---|---|
| synthyrion_btab: bounded | 36 |
| synthyrion_btab: refused | 3 |
| synthyrion_capauth: bounded | 33 |
| synthyrion_capauth: refused | 13 |
| synthyrion_gatecases: bounded | 47 |
| synthyrion_gatecases: refused | 69 |
| synthyrion_kcrypto: bounded | 11 |
| synthyrion_kcrypto: refused | 1 |
| synthyrion_kheal: bounded | 138 |
| synthyrion_kheal: refused | 45 |
| synthyrion_pmm: bounded | 15 |
| synthyrion_pmm: refused | 5 |
| synthyrion_ring: bounded | 32 |
| synthyrion_ring: refused | 6 |
| synthyrion_rv: bounded | 46 |
| synthyrion_rv: refused | 69 |
| synthyrion_sched_rt: bounded | 60 |
| synthyrion_sched_rt: refused | 32 |
| synthyrion_smp: bounded | 111 |
| synthyrion_smp: refused | 135 |
| synthyrion_vmm: bounded | 145 |
| synthyrion_vmm: refused | 227 |
| hypervisor: bounded | 205 |
| hypervisor: refused | 138 |
| microkernel: bounded | 2341 |
| microkernel: refused | 1180 |

Functions per crate

## Standards and assurance status /assurance/status

| Standard | Status today | What is true |
|---|---|---|
| FIPS 140-3 (cryptographic module validation) | NOT VALIDATED | No module has been submitted for validation. The signature algorithm ML-DSA-87 (FIPS 204) is implemented; implementing an algorithm is not a validation. |
| Common Criteria (ISO/IEC 15408) | NOT EVALUATED | No evaluation has been performed. |
| Government or safety certification | NONE | Synthyrion Core L5 holds no safety, security or government certification. |
| Whole-kernel functional-correctness proof | NOT DONE | 0 unbounded functional-correctness proofs are recorded (measured 2026-11-09). Weaker evidence is listed, with its strength, on the assurance page. |

Standards: status today

| Platform | Row status | Strength of evidence |
|---|---|---|
| x86-64 UEFI F25a | Done, limit stated | virtual machine, with some silicon runs |
| AArch64 F25b | Done, limit stated | virtual machine; kernel logic tests ran as a user program on silicon |
| AArch32 F25c | Done, limit stated | virtual machine only |
| RISC-V 64 F25d | Done, limit stated | virtual machine only |

Evidence strength by platform

Status from the kernel status table, counted 2026-11-19. A virtual machine is not hardware.

## § 1 The four strengths

tested

A property checked on examples. A sample, not a proof. The kernel test suite is the evidence.

- 892 pass
bounded

Checked exhaustively over every case up to a stated size, comparing the real kernel to a model. A proof about that finite domain only.

- 13 checks
panic-free

Shown for all inputs that named code cannot panic. Says nothing about whether it does the right thing. Scope: V-548 9 of 10 functions, V-552 the 4 loops by induction, V-553 `synthyrion_caps` 29 of 29.

- 3 proofs
functionally proven

A machine-checked proof, for all inputs, that the code does what its specification says.

- 0 proofs

These four counts are read from the project’s equivalence table’s own measurement section, measured 2026-11-09; every other figure on this page carries its own date and source (section 4). A bounded check is a proof about a finite domain only: a property true for three objects can fail at four. The first-party checker that runs these checks is not itself verified.

**These four counts are older than the evidence below.** The tested, bounded, panic-free and proven counts above are read from the equivalence table’s own measurement section, which was last measured 2026-11-09; the newest evidence below is dated 2026-11-27. Where the two differ, the dated figures below are the newer.

## § 2 The eight assurance goals

Each is a goal Synthyrion Core L5 sets itself. The status and the strength are read from the project’s own table, measured 2026-11-19; they change when the table changes.

| ID | Goal | Status | Strength today | Achieved | Not yet achieved |
|---|---|---|---|---|---|
| S1 | Abstract specification A complete, machine-checked specification of what the kernel is allowed to do, written in a theorem prover, against which the code can be checked. | In progress strength: a prover-checked SPECIFICATION of a slice of the kernel; no refinement from the code | no row at the functionally-proven level (the code is not refined to the spec) | An abstract specification of the kernel's own objects exists in the Isabelle prover: the kernel state, capabilities and their derivation tree, retype, the scheduler and a 14-operation invocation layer. Proved on it: the capability and IPC invariants, integrity, and non-interference. | The Rust code is not proved to refine the specification, and the specification still differs from the kernel in listed places. Its IPC models synchronous endpoints, not the kernel's inbox IPC, and some objects are left out: mapping and address spaces, call and reply, and parts of fault, interrupt and virtual-CPU handling. Aligning it is planned after the kernel lands. |
| S2 | Executable specification A precise model of the kernel that can be run, tied to the real code by exhaustive checks, so that any difference between them shows up as a failure. | In progress | bounded | Executable models of four kernel subsystems are tied to the real kernel code by five bounded exhaustive checks, to depths of 4 to 8. | There is no model of the whole kernel, and a bounded check speaks only for the sizes it names. |
| S3 | Functional-correctness refinement to the implementation A proof, for all inputs, that the kernel's code does exactly what the specification says. | In progress | bounded | The real kernel is checked against the models by forward simulation over finite domains: every case up to a stated size. These checks have found 8 defects. | No proof for all inputs exists. A bounded check is a proof about its domain only: a property true for three objects can fail at four. |
| S4 | Binary / translation validation A check that the machine code the compiler produces does what the source says, so that the compiler does not have to be trusted. | In progress strength: BOUNDED where loops are unrolled, otherwise per function; 2 of 276 kernel functions; NOT a whole-frozen-object result | bounded where loops are unrolled, otherwise per function (2 of 276; not a whole-object result) | An experimental tool checks single compiled x86-64 functions against their source form, with two independent solvers required to agree. When last measured it validated 2 of the kernel's 276 functions. The Rust compiler and its code generator remain trusted. | 274 of the 276 kernel functions have no binary validation, and the result is per function, not for the whole compiled kernel. |
| S5 | Integrity A theorem that no task can change anything it holds no authority over. | In progress | bounded at code level | In the Isabelle prover, on the abstract specification, an unbounded theorem shows that every user operation changes only what the acting thread's authority permits. The code's enforcement points are also checked exhaustively over finite domains: no message is delivered without a live capability that grants write, and the consent and revocation rules hold in every case up to the stated size. | The theorem is about the specification, not the Rust code, and its send, receive and wait cases model synchronous endpoints, not the kernel's inbox IPC, so it does not cover the kernel's IPC. At code level the checks are bounded, up to three tasks. |
| S6 | Confidentiality / non-interference A theorem that what one domain does cannot be observed by a domain it is meant to be isolated from. | In progress | bounded at code level (scheduler only) | A machine-checked proof, in the Isabelle prover, of non-interference for an abstract model of the kernel that covers ticks, scheduling, signals, copy and mint, move, delete, revoke and retype, and send, receive and wait on synchronous endpoints. An exhaustive check over a bounded domain shows that the real scheduler's choices do not leak between isolated partitions; deliberately leaky schedulers are refuted. | The Isabelle proof is about the abstract model, not the code, and its send, receive and wait cases are not the kernel's inbox IPC, so non-interference for the kernel's IPC is not established. The exhaustive check covers the scheduler only: nothing yet for message passing, memory or capabilities in the code. There is no model of deliberate declassification. Timing channels below the architecture are a separate, measured claim. |
| S7 | capDL system-initialisation proof A proof that the system starts in exactly the configuration its description says, with no authority that was not meant to be there. | In progress | tested (fixtures only) | A description language and a checker for the initial system configuration. It refuses configurations that grant forbidden authority, checks the exact build, and convicts planted widened, extra-capability and leaking configurations. It is tested on fixtures. | It has not been run on the real boot distribution, and there is no verified initialiser. |
| S8 | WCET Published upper bounds on how long kernel operations can take, so that real-time deadlines can be shown to be met. | In progress | tested (measured only) | A first-party static analyser computes worst-case bounds. One kernel function is bounded in processor cycles under stated assumptions, and kernel running times are measured. | Bounds for the kernel's entry points are pending, with the reason named for each. Outside timing-analysis tools are planned, not run. |

The eight assurance goals

## § 3 Stricter goals

These go beyond the eight above. Where rows of the project’s table back a goal, its status is read from them; the others are stated with what is and is not done.

- [x86-64](https://synthyrion.com/core/features.html#f25a)
- [AArch64](https://synthyrion.com/core/features.html#f25b)
- [AArch32](https://synthyrion.com/core/features.html#f25c)
- [RISC-V 64](https://synthyrion.com/core/features.html#f25d)
- [Virtualization: virtual CPU object](https://synthyrion.com/core/features.html#f22a)
- [Virtualization: second-level page tables](https://synthyrion.com/core/features.html#f22b) (ARM at VM strength, no ARM silicon; x86 as stated)
- [Virtualization: virtual interrupts](https://synthyrion.com/core/features.html#f22c) (ARM at VM strength, no ARM silicon; x86 as stated)
- [Multi-core, proven on real hardware](https://synthyrion.com/core/features.html#f23)
- [As a guest on Linux hosts](https://synthyrion.com/core/features.html#f26a)
- [As a guest on Windows hosts](https://synthyrion.com/core/features.html#f26b)
- [Hosting a Linux guest](https://synthyrion.com/core/features.html#f27a)

- [Hosting a Windows guest](https://synthyrion.com/core/features.html#f27b) — planned

| ID | Goal | Status | Strength today | Achieved | Not yet achieved |
|---|---|---|---|---|---|
| G1 | Post-quantum cryptography only, with keys of at least 512 bits Every symmetric key is at least 512 bits, key exchange and signatures are post-quantum with no hybrids, and hashes are SHA-512 or stronger. | In progress | tested | Signatures (ML-DSA-87) and SHA-512 hashing are implemented in first-party Rust at the highest standard parameter sets, and key exchange (ML-KEM-1024) exists in the development tree but is not part of the 0.1.3 package. The web store seals its own files with a first-party construction under 512-bit keys (SHA-512, HMAC-SHA-512 and SHAKE256); it is not part of the kernel package, is not a standardised mode, and has had no independent review. Key exchange and authentication between Synthyrion components are post-quantum only, with no hybrid mode. | The crypto-floor check run on 2026-11-27 (source commit 0368426a0) lists 34 remaining sites below the 512-bit symmetric floor, in 5 files: the TLS 1.3, TLS-session and Tor onion code (where the peer fixes a 256-bit cipher), the legacy sealing primitive kept only for those users, and the conformance-test runner. The key vault and measured boot now seal with SEAL512 (a SHAKE256 keystream and an HMAC-SHA-512 tag under 512-bit keys; a first-party construction, not a standardised mode, no independent review) and keep only a read-only opener for old 256-bit ChaCha20 blobs. The remaining ChaCha20 sites are not linked into the signed 0.1.3 image, which contains no cipher. Re-encrypting existing stored keys is a deliberate step, done with backups. None of this is FIPS 140-3 validated. |
| G2 | No third-party code in the kernel Every crate the kernel is built from is written in-house; third-party software runs only in applications above the operating system. | Done | measured | The kernel is written entirely in Rust by the project, and the list of crates it is built from contains only the project's own crates. The list is checked by a test of this site's build, which fails if a third-party dependency appears. | This is a statement about the source. The Rust compiler and its code generator are third-party tools and are trusted until the binary-level check (S4) exists. |
| G3 | Panic-freedom proofs for kernel code A machine-checked proof, for all inputs, that named kernel code cannot panic. | In progress | panic-free | 3 panic-freedom proofs exist, produced by the project’s own first-party checker. Scope: V-548 9 of 10 functions, V-552 the 4 loops by induction, V-553 `synthyrion_caps` 29 of 29. | A panic-freedom proof says nothing about whether the code does the right thing. The checker is itself not verified, and part of the kernel is not yet covered. |
| G4 | A bound on the running time of every kernel entry point A proven worst-case time, in processor cycles, for every call into the kernel. | In progress | tested (measured only) | A first-party static analyser computes worst-case bounds and refuses code it cannot bound (an undeclared loop, an uncosted callee, recursion). One kernel function is bounded in processor cycles on the compiled code, under stated assumptions. | Most kernel entry points are not yet bounded; for each, the blocker is named in the project's generated timing table. The cycle bounds cover Intel cores only, and no interference between cores is proven. |
| G5 | A kernel that detects and repairs damage to itself Bit flips and tampering in the kernel's own tables and image are detected, repaired when a copy can be proven right, and contained when not. | In progress | tested | Page tables and capability tables carry check words; a flipped bit is detected on read (the read fails closed); background repair by a scrubber is implemented and tested and is not yet run on a timer in the released image; when it runs, a flip is repaired in place when one copy can be proven right and contained when not. Planted flips are caught in tests. A position-independent early-boot image picks a different load address on each boot and refuses an image with a flipped bit; this runs in an emulator. | The check words catch accidents, not deliberate tampering. Object headers, the kernel heap and code, and the hardware copy of the page tables are not covered. The later stages of the plan, several copies that vote and replace a damaged one, are not built. |
| G6 | Every proof and check shown able to fail Each proof or check is accompanied by a deliberately planted fault that it catches, so that a check that cannot fail is not counted as evidence. | In progress | tested | New proofs and checks are submitted with planted faults that make them fail, removed afterwards. Many of the existing ones carry such plants, for example the isolation checks refuse deliberately leaky schedulers, and the self-repair checks catch thousands of planted bit flips. | The rule is applied check by check. Checks that turned out unable to fail have been found and repaired, and the search for more continues. |
| G7 | Runs on x86-64, AArch64 and RISC-V 64, as a guest and as a host, on real multi-core hardware The same kernel on three processor families, with virtualization, and proven on real hardware rather than only in an emulator. | In progress | per row: host, virtual machine or hardware, as labelled on the platforms page |  |  |

Synthyrion's stricter goals

## § 4 The evidence, with its dates

Every figure here is read from a file of the project when this page is built, and carries the date it was measured and the file it came from. **Counted** figures are counted from the files at build time; **recorded** figures are results a project member wrote into its progress document after running a tool: this build does not re-run those tools, and the full run of them is held until the kernel is finished, so each recorded figure says it is re-measured at the final run. The date of a recorded figure is the latest dated entry of the progress document that describes those files or that result. A figure is never typed into the page by hand.

| Figure | Value | Measured | Read from | How |
|---|---|---|---|---|
| Isabelle theories (the specification and its proofs) | 73 | 2026-11-27 | `proofs/isabelle/*.thy` | counted |
| Isabelle lines | 24,260 | 2026-11-27 | `proofs/isabelle/*.thy` | counted |
| Isabelle theorem, lemma and corollary statements | 1,285 | 2026-11-27 | `proofs/isabelle/*.thy` | counted |
| sorry, oops or axiomatization in those theories | 0 | 2026-11-27 | `proofs/isabelle/*.thy` | counted |
| Isabelle plant theories (deliberately false statements the prover must reject) | 100 | 2026-11-27 | `proofs/isabelle, files named Plant` | counted |
| Last recorded full Isabelle build | session built and finished (0:00:40 elapsed, 2 threads) | 2026-11-04 | `the Isabelle progress record` | recorded; re-measured at the final run |
| Lean files (without plants) | 52 | 2026-11-27 | `proofs/lean (without plants and superseded)` | counted |
| Lean lines | 8,554 | 2026-11-27 | `proofs/lean (without plants and superseded)` | counted |
| Lean theorem and lemma declarations | 303 | 2026-11-27 | `proofs/lean (without plants and superseded)` | counted |
| sorry in Lean code | 0 | 2026-11-27 | `proofs/lean (without plants and superseded)` | counted |
| Lean plant files | 45 | 2026-11-27 | `proofs/lean/**/plants` | counted |
| Last recorded Lean axiom scan (tree at commit b5e3788b7) | 762 theorems under Synthyrion, none uses a non-standard axiom | 2026-11-04 | `the Lean progress record` | recorded; re-measured at the final run |
| Verus models recorded verified | 6 (verified counts 43, 50, 44, 93, 24, 20; 0 errors) | 2026-10-19 | `the Verus progress record` | recorded; re-measured at the final run |
| Verus plants (a wrong version Verus must reject) | 73 | 2026-11-27 | `the list of Verus plants` | counted |
| Verus proof files paired with shipped code | 4 | 2026-11-27 | `the list of Verus proof copies and the shipped code they pair with` | counted |
| Kernel-math census: functions | 5,659 | 2026-11-26 | `the census of kernel functions (totals line)` | recorded; re-measured at the final run |
| Kernel-math census: panic-free proven | 5,101 (plus 426 by induction); 105 blocked | 2026-11-26 | `the census of kernel functions (totals line)` | recorded; re-measured at the final run |

Proof evidence, measured

### What these figures do and do not show

- **Specification level, not code level.** The Isabelle and Lean results are about specifications of the kernel. The Rust kernel is not proved to refine them. They show the specification is consistent and has the stated properties; they do not show the code has them.
- **Isabelle IPC model, corrected on 2026-11-04.** The Isabelle send, receive and wait cases model synchronous endpoints (blocking sends and receives in queues), not the kernel’s inbox IPC (a task inbox, call and reply with a one-shot reply capability, capability transfer only over a Grant endpoint). So the Isabelle integrity and non-interference results do not cover the kernel’s IPC. The scheduler, signal and capability-operation cases are unaffected, at specification level.
- **Non-interference over the kernel’s IPC is open.** It is not established at specification level or at code level.
- **The specification still differs from the kernel** in listed places, and is being aligned to the kernel after it lands.
- **Verus** proves properties of proof copies of named shipped functions, checked token-identical to the shipped code, with plants that must be rejected. It covers those functions only. **Bounded checks** speak for the sizes they name. **Panic-free** says nothing about correctness. The census counts functions the project’s own checker finds panic-free; that checker is not itself verified.
- **Recorded runs are not re-run by this site.** A recorded figure is as old as the document that records it; its date is shown.

### The latest runs on the build host

The build host is a virtual machine on one of the project’s servers; it is not bare metal. Each run copies one commit of the kernel source into the virtual machine and runs one check at a time. The tables below are a copy of the run’s own summary, labelled with the run time and the commit it ran on. This site does not re-run them, and a change made after that commit is not in them.

- **Isabelle and Lean prove the model only.** 18 of 18 Isabelle and Lean checks passed in run 2026-11-12 01:33:14. They prove a specification of the kernel, not the kernel. The Isabelle model differs from the kernel in 14 of 16 operations. A green row here says the model is consistent and has the stated properties; it says nothing about the Rust code.
- **The Verus proof copies are stale.** The check that the proof copies are token-identical to the shipped code failed in run 2026-11-12 01:33:14 with 162 differences, all in one pair (the virtual-memory proof copy lacks functions the shipped code has). The Verus runs that pass prove the proof copies, so for the stale copy they say nothing about the shipped code until it is refreshed.
- **4 of 5 Kani harnesses fail.** In run 2026-11-12 01:33:14 they fail on an unwinding assertion (the loop bound set for them is no longer enough). None of the harnesses is in the microkernel: they cover two helper crates.
- **11 checks did not run** in run 2026-11-12 01:33:14 (they were held back by the build host’s free-disk floor). A check that did not run is not a pass.
- **The behaviour-equivalence suite failed on its last recorded run.** As of 2026-11-12 (main f94f7de32; not re-run since), 24 of 73 of its non-exhaustive checks fail. All 24 are page-count checks that come out one to two frames lower than they expect; the cause was being diagnosed at that date. The 5 slow exhaustive checks had not finished, so they have no result yet. No status row is marked done on the strength of this suite while checks fail.

#### Kernel suite: the real microkernel and its boot scenes

Run 2026-11-12 03:03:08 on main commit `68f1d7d965bd`, offline. pass 16 fail 1 timeout 0 did not run 0. A test that passes is a test result, not a proof.

| Check | Result | What it checks | Detail |
|---|---|---|---|
| A01 | pass | microkernel: lib + the named integration groups that exist on main (fault_injection); absent on main: sc_store sc_store_scan | 274 tests pass, 0 fail, 1 ignored |
| A02 | pass | synthyrion_wcetan: lib + loop_capacity, core_twins, real_core, duplicate_keys | 75 tests pass, 0 fail, 0 ignored |
| A03 | pass | synthyrion_gatecases: table + lean_seal (the table-driven test of the kernel gate) | 14 tests pass, 0 fail, 0 ignored |
| A04 | pass | synthyrion_wcetan check (count from the build host, informational) |  |
| A05 | pass | synthyrion_wcetan missing --all --cross-check (count from the build host, informational) |  |
| A06 | pass | boot: gate_uefi 66/66 (QEMU TCG in a virtual machine, not bare metal) | 66 of 66 boot-scene rows pass |
| A07 | pass | boot: rootas 49/49 with the PREFLIGHT line (QEMU TCG in a virtual machine) | 49 of 49 boot-scene rows pass |
| A08 | pass | boot: AArch64 --fast 2/2, EL1 + EL2, Synthyrion-A64-BOOT-OK (QEMU_AARCH64 = the build host wrapper) | 2 of 2 boot-scene rows pass |
| A09 | pass | boot: lean release image, whole PASS (QEMU TCG in a virtual machine) | 8 of 8 boot-scene rows pass |
| A10 | pass | cargo check: boot_uefi |  |
| A11 | pass | cargo check: boot_aarch64 |  |
| A12 | pass | cargo check: boot_aarch64_uefi |  |
| A13 | fail | cargo check: boot_riscv64 | rc=101 error[E0599]: no method named ept_model found for reference &microkernel::Kernel in the current scope elapsed=5s :: cargo check in boot_riscv64 |
| A14 | pass | cargo check: boot_riscv64_uefi |  |
| A15 | pass | cargo check: boot_aarch32 |  |
| A16 | pass | cargo check: boot_aarch32_uefi |  |
| A17 | pass | cargo check: a boot program that is not part of the published image set |  |
| A01 | 275 | 0 | 1 |
| A02 | 75 | 0 | 0 |
| A03 | 14 | 0 | 0 |
| A06 | 66 | 0 | 0 |
| A07 | 49 | 0 | 0 |
| A08 | 2 | 0 | 0 |
| A09 | 8 | 0 | 0 |

Run 2026-11-12 03:03:08

#### Proof suite: Verus, Kani, Isabelle, Lean and the project’s own checkers

Run 2026-11-12 01:33:14 on main commit `9f63b37a7b74`, online. pass 27 fail 6 timeout 0 did not run 11. A test that passes is a test result, not a proof.

| Check | Result | What it checks | Detail |
|---|---|---|---|
| P3.0 | pass | Verus tool pins (SHA-512 against the pinned values) and Z3 version |  |
| P3.1 | pass | Verus verus/caps/caps.rs | 43 verified, 0 errors |
| P3.2 | pass | Verus verus/pmm/pmm.rs | 50 verified, 0 errors |
| P3.3 | pass | Verus verus/vmm/vmm.rs | 93 verified, 0 errors |
| P3.4 | pass | Verus verus/pure/pure.rs | 24 verified, 0 errors |
| P3.5 | pass | Verus cspace_model.rs | 20 verified, 0 errors |
| P3.id | fail | Verus identity gate: proof copy == shipped code (caps, pmm, vmm, pure) | rc=1 RED: proof copy differs from shipped code (162 problem(s) over 4 pair(s)) |
| P3.st | fail | Verus identity gate selftest: planted drift must go RED | rc=1 clean verus/caps/caps.rs -> GREEN |
| P3.plants | pass | Verus plants (all of plants.tsv) | 73 planted faults, all caught |
| P4.1 | pass | Kani harness synthyrion_verify::for_all_in_is_sound | verification successful |
| P4.2 | fail | Kani harness synthyrion_provision::enabling_a_scope_is_observable | rc=1 elapsed=18s Failed Checks: unwinding assertion loop 0 |
| P4.3 | fail | Kani harness synthyrion_provision::capability_grant_is_exact | rc=1 elapsed=20s Failed Checks: unwinding assertion loop 0 |
| P4.4 | fail | Kani harness synthyrion_provision::removal_strips_every_capability | rc=1 elapsed=23s Failed Checks: unwinding assertion loop 0 |
| P4.5 | fail | Kani harness synthyrion_provision::outsider_has_no_capability | rc=1 elapsed=20s Failed Checks: unwinding assertion loop 0 |
| P1.SYNTHYRION_Core | pass | Isabelle session SYNTHYRION_Core |  |
| P1.SYNTHYRION_InboxK | pass | Isabelle session SYNTHYRION_InboxK |  |
| P1.SYNTHYRION_NICallK | pass | Isabelle session SYNTHYRION_NICallK |  |
| P1.SYNTHYRION_NICompCK | pass | Isabelle session SYNTHYRION_NICompCK |  |
| P1.SYNTHYRION_NIExitCK | pass | Isabelle session SYNTHYRION_NIExitCK |  |
| P1.SYNTHYRION_NIIrqK | pass | Isabelle session SYNTHYRION_NIIrqK |  |
| P1.SYNTHYRION_CSpaceK | pass | Isabelle session SYNTHYRION_CSpaceK |  |
| P1.SYNTHYRION_NICapXK | pass | Isabelle session SYNTHYRION_NICapXK |  |
| P1.SYNTHYRION_CSpaceKX | pass | Isabelle session SYNTHYRION_CSpaceKX |  |
| P1.SYNTHYRION_CSpaceRetype | pass | Isabelle session SYNTHYRION_CSpaceRetype |  |
| P1.SYNTHYRION_CSpaceKSlot | pass | Isabelle session SYNTHYRION_CSpaceKSlot |  |
| P1.SYNTHYRION_NIScK | pass | Isabelle session SYNTHYRION_NIScK |  |
| P1.SYNTHYRION_NI3 | pass | Isabelle session SYNTHYRION_NI3 |  |
| P1.SYNTHYRION_CSpaceKPre | pass | Isabelle session SYNTHYRION_CSpaceKPre |  |
| P2.1 | pass | Lean lake build of Synthyrion (incl. its axiom scan) |  |
| P2.2 | pass | Lean plants P*.lean: every plant must fail to build | 34 of 34 plants FAILED to build with a Lean error |
| P2.3 | pass | Lean controls C*.lean: every control must build | 11 of 11 controls C*.lean built |
| P5.1 | pass | synthyrion_prover (MIR panic-freedom prover) tests | 37 tests pass, 0 fail, 0 ignored |
| P5.2 | pass | synthyrion_kmath slot_models (bounded refinement of the slot models against the kernel) | 5 tests pass, 0 fail, 0 ignored |
| P5.3 | did not run | microkernel kernel_math (kernel-math bounded refinement checks) | disk floor: 16 GB free |
| P5.4 | did not run | microkernel kernel_math_ipc (kernel-math bounded refinement checks) | disk floor: 16 GB free |
| P5.5 | did not run | microkernel kernel_math_live (kernel-math bounded refinement checks) | disk floor: 16 GB free |
| P5.6 | did not run | microkernel kernel_math_loops (kernel-math bounded refinement checks) | disk floor: 16 GB free |
| P5.7 | did not run | microkernel kernel_math_mkproof (kernel-math bounded refinement checks) | disk floor: 16 GB free |
| P5.8 | did not run | microkernel kernel_math_sep (kernel-math bounded refinement checks) | disk floor: 16 GB free |
| P5.9 | did not run | microkernel kernel_math_smp (kernel-math bounded refinement checks) | disk floor: 16 GB free |
| P5.10 | did not run | microkernel kernel_math_smp_sched (kernel-math bounded refinement checks) | disk floor: 16 GB free |
| P5.11 | did not run | microkernel fastpath (IPC fastpath vs spec) | disk floor: 16 GB free |
| P5.12 | did not run | microkernel gate_fast (exhaustive) | disk floor: 16 GB free |
| P5.13 | did not run | microkernel kernel_equivalence (78 tests, last measured 7,188 s) | disk floor: 16 GB free |
| P3 | 73 | 0 | 0 |
| P2 | 45 | 0 | 0 |
| P5 | 42 | 0 | 0 |

Run 2026-11-12 01:33:14

## § 5 How to read these goals

- A goal that is not achieved is not described as achieved. The status of each of the eight assurance goals is copied from the project’s own table by the program that generates this page; it is not edited by hand, so it cannot run ahead of the work.
- Rust’s type system removes whole classes of memory errors, and panic-freedom proofs cover parts of the capability code. Neither is a statement that the kernel does what its specification says.
- The Rust compiler and its code generator are in the trusted base until goal S4 is achieved.

When a functional-correctness proof exists, this page will say so, with its scope, and the status of goal S3 will change by itself.
