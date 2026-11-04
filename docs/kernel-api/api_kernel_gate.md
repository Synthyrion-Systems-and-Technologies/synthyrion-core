# Kernel gate reference

Every call of the kernel gate, in groups, generated from the ABI definition (selector, capability, parameters, result) and explained call by call. 112 calls: 104 wired, 1 defined but not served, 7 superseded.

Read [Calling the kernel](calling.md) first: it explains the register convention, capability addresses and depth, and the status words that every entry below refers to.

## Index

| selector | call | group | status |
|---|---|---|---|
| 0 | [`yield`](#yield-kernel-gate-selector-0) | Basic application calls | Wired |
| 1 | [`send`](#send-kernel-gate-selector-1) | Basic application calls | Wired |
| 2 | [`recv`](#recv-kernel-gate-selector-2) | Basic application calls | Wired |
| 3 | [`map_memory`](#map_memory-kernel-gate-selector-3) | Basic application calls | Wired |
| 4 | [`unmap`](#unmap-kernel-gate-selector-4) | Basic application calls | Wired |
| 5 | [`vault_op`](#vault_op-kernel-gate-selector-5) | Basic application calls | Wired |
| 6 | [`exit`](#exit-kernel-gate-selector-6) | Basic application calls | Wired |
| 32 | [`set_partition_schedule`](#set_partition_schedule-kernel-gate-selector-32) | Priorities and partitions | Wired |
| 33 | [`reset_partition_schedule`](#reset_partition_schedule-kernel-gate-selector-33) | Priorities and partitions | Wired |
| 34 | [`call`](#call-kernel-gate-selector-34) | Messages, calls and replies | Wired |
| 35 | [`reply`](#reply-kernel-gate-selector-35) | Messages, calls and replies | Wired |
| 36 | [`send_caps`](#send_caps-kernel-gate-selector-36) | Messages, calls and replies | Wired |
| 37 | [`receive`](#receive-kernel-gate-selector-37) | Messages, calls and replies | Wired |
| 38 | [`signal`](#signal-kernel-gate-selector-38) | Notifications | Wired |
| 39 | [`wait`](#wait-kernel-gate-selector-39) | Notifications | Wired |
| 40 | [`bind_notification`](#bind_notification-kernel-gate-selector-40) | Notifications | Wired |
| 41 | [`unbind_notification`](#unbind_notification-kernel-gate-selector-41) | Notifications | Wired |
| 42 | [`bind_irq`](#bind_irq-kernel-gate-selector-42) | Interrupts | Superseded |
| 43 | [`unbind_irq`](#unbind_irq-kernel-gate-selector-43) | Interrupts | Superseded |
| 44 | [`set_fault_handler`](#set_fault_handler-kernel-gate-selector-44) | Faults and restart | Wired |
| 45 | [`clear_fault_handler`](#clear_fault_handler-kernel-gate-selector-45) | Faults and restart | Wired |
| 46 | [`restart_task`](#restart_task-kernel-gate-selector-46) | Faults and restart | Wired |
| 47 | [`move_task_partition`](#move_task_partition-kernel-gate-selector-47) | Priorities and partitions | Wired |
| 48 | [`retype`](#retype-kernel-gate-selector-48) | Untyped memory and retype | Wired |
| 49 | [`revoke_untyped_step`](#revoke_untyped_step-kernel-gate-selector-49) | Untyped memory and retype | Superseded |
| 50 | [`destroy_root_untyped`](#destroy_root_untyped-kernel-gate-selector-50) | Untyped memory and retype | Wired |
| 51 | [`create_sched_context`](#create_sched_context-kernel-gate-selector-51) | Scheduling contexts | Wired |
| 52 | [`bind_sched_context`](#bind_sched_context-kernel-gate-selector-52) | Scheduling contexts | Wired |
| 53 | [`unbind_sched_context`](#unbind_sched_context-kernel-gate-selector-53) | Scheduling contexts | Wired |
| 54 | [`reply_recv`](#reply_recv-kernel-gate-selector-54) | Messages, calls and replies | Wired |
| 55 | [`mint_badged_endpoint`](#mint_badged_endpoint-kernel-gate-selector-55) | CNodes and capability slots | Superseded |
| 56 | [`set_task_priority`](#set_task_priority-kernel-gate-selector-56) | Priorities and partitions | Wired |
| 57 | [`set_task_mcp`](#set_task_mcp-kernel-gate-selector-57) | Priorities and partitions | Wired |
| 58 | [`set_task_mcp_sealed`](#set_task_mcp_sealed-kernel-gate-selector-58) | Priorities and partitions | Wired |
| 59 | [`clear_task_priority_sealed`](#clear_task_priority_sealed-kernel-gate-selector-59) | Priorities and partitions | Wired |
| 60 | [`cnode_install_initial`](#cnode_install_initial-kernel-gate-selector-60) | CNodes and capability slots | Wired |
| 61 | [`cnode_copy`](#cnode_copy-kernel-gate-selector-61) | CNodes and capability slots | Wired |
| 62 | [`cnode_mint`](#cnode_mint-kernel-gate-selector-62) | CNodes and capability slots | Wired |
| 63 | [`cnode_move`](#cnode_move-kernel-gate-selector-63) | CNodes and capability slots | Wired |
| 64 | [`cnode_mutate`](#cnode_mutate-kernel-gate-selector-64) | CNodes and capability slots | Wired |
| 65 | [`cnode_delete`](#cnode_delete-kernel-gate-selector-65) | CNodes and capability slots | Wired |
| 66 | [`cnode_revoke_step`](#cnode_revoke_step-kernel-gate-selector-66) | CNodes and capability slots | Wired |
| 67 | [`cnode_lookup`](#cnode_lookup-kernel-gate-selector-67) | CNodes and capability slots | Wired |
| 68 | [`slot_send`](#slot_send-kernel-gate-selector-68) | Messages, calls and replies | Wired |
| 69 | [`hv_protect`](#hv_protect-kernel-gate-selector-69) | Virtual machines (VCPU and EPT/NPT) | Wired |
| 70 | [`ept_create`](#ept_create-kernel-gate-selector-70) | Virtual machines (VCPU and EPT/NPT) | Superseded |
| 71 | [`ept_map`](#ept_map-kernel-gate-selector-71) | Virtual machines (VCPU and EPT/NPT) | Superseded |
| 72 | [`ept_unmap`](#ept_unmap-kernel-gate-selector-72) | Virtual machines (VCPU and EPT/NPT) | Wired |
| 73 | [`ept_access`](#ept_access-kernel-gate-selector-73) | Virtual machines (VCPU and EPT/NPT) | Wired |
| 74 | [`vcpu_create`](#vcpu_create-kernel-gate-selector-74) | Virtual machines (VCPU and EPT/NPT) | Superseded |
| 75 | [`vcpu_set_regs`](#vcpu_set_regs-kernel-gate-selector-75) | Virtual machines (VCPU and EPT/NPT) | Wired |
| 76 | [`vcpu_regs`](#vcpu_regs-kernel-gate-selector-76) | Virtual machines (VCPU and EPT/NPT) | Wired |
| 77 | [`vcpu_exit_info`](#vcpu_exit_info-kernel-gate-selector-77) | Virtual machines (VCPU and EPT/NPT) | Wired |
| 78 | [`vcpu_start`](#vcpu_start-kernel-gate-selector-78) | Virtual machines (VCPU and EPT/NPT) | Wired |
| 79 | [`vcpu_destroy`](#vcpu_destroy-kernel-gate-selector-79) | Virtual machines (VCPU and EPT/NPT) | Wired |
| 80 | [`vcpu_state`](#vcpu_state-kernel-gate-selector-80) | Virtual machines (VCPU and EPT/NPT) | Wired |
| 81 | [`vcpu_exit_count`](#vcpu_exit_count-kernel-gate-selector-81) | Virtual machines (VCPU and EPT/NPT) | Wired |
| 82 | [`tcb_suspend`](#tcb_suspend-kernel-gate-selector-82) | Task control | Wired |
| 83 | [`tcb_resume`](#tcb_resume-kernel-gate-selector-83) | Task control | Wired |
| 84 | [`tcb_write_registers`](#tcb_write_registers-kernel-gate-selector-84) | Task control | Wired |
| 85 | [`tcb_read_registers`](#tcb_read_registers-kernel-gate-selector-85) | Task control | Wired |
| 86 | [`tcb_configure`](#tcb_configure-kernel-gate-selector-86) | Task control | Wired |
| 87 | [`map_frame`](#map_frame-kernel-gate-selector-87) | Address spaces, frames and caches | Wired |
| 88 | [`freeze_frame`](#freeze_frame-kernel-gate-selector-88) | Address spaces, frames and caches | Defined |
| 89 | [`asid_assign`](#asid_assign-kernel-gate-selector-89) | Address spaces, frames and caches | Wired |
| 90 | [`frame_flush`](#frame_flush-kernel-gate-selector-90) | Address spaces, frames and caches | Wired |
| 91 | [`frame_unify`](#frame_unify-kernel-gate-selector-91) | Address spaces, frames and caches | Wired |
| 92 | [`frame_unmap`](#frame_unmap-kernel-gate-selector-92) | Address spaces, frames and caches | Wired |
| 93 | [`iospace_create`](#iospace_create-kernel-gate-selector-93) | DMA address spaces (IO spaces) | Wired |
| 94 | [`iospace_attach_device`](#iospace_attach_device-kernel-gate-selector-94) | DMA address spaces (IO spaces) | Wired |
| 95 | [`iospace_map`](#iospace_map-kernel-gate-selector-95) | DMA address spaces (IO spaces) | Wired |
| 96 | [`iospace_unmap`](#iospace_unmap-kernel-gate-selector-96) | DMA address spaces (IO spaces) | Wired |
| 97 | [`iospace_invalidate`](#iospace_invalidate-kernel-gate-selector-97) | DMA address spaces (IO spaces) | Wired |
| 98 | [`iospace_destroy`](#iospace_destroy-kernel-gate-selector-98) | DMA address spaces (IO spaces) | Wired |
| 99 | [`set_task_passive`](#set_task_passive-kernel-gate-selector-99) | Priorities and partitions | Wired |
| 100 | [`bind_sched_context_notification`](#bind_sched_context_notification-kernel-gate-selector-100) | Scheduling contexts | Wired |
| 101 | [`configure_sched_context`](#configure_sched_context-kernel-gate-selector-101) | Scheduling contexts | Wired |
| 102 | [`set_timeout_handler`](#set_timeout_handler-kernel-gate-selector-102) | Faults and restart | Wired |
| 103 | [`clear_timeout_handler`](#clear_timeout_handler-kernel-gate-selector-103) | Faults and restart | Wired |
| 104 | [`sched_context_consumed`](#sched_context_consumed-kernel-gate-selector-104) | Scheduling contexts | Wired |
| 105 | [`sched_context_yield_to`](#sched_context_yield_to-kernel-gate-selector-105) | Scheduling contexts | Wired |
| 106 | [`asid_pool_make`](#asid_pool_make-kernel-gate-selector-106) | Address spaces, frames and caches | Wired |
| 110 | [`ioport_issue`](#ioport_issue-kernel-gate-selector-110) | I/O ports | Wired |
| 111 | [`ioport_in`](#ioport_in-kernel-gate-selector-111) | I/O ports | Wired |
| 112 | [`ioport_out`](#ioport_out-kernel-gate-selector-112) | I/O ports | Wired |
| 113 | [`ioport_revoke`](#ioport_revoke-kernel-gate-selector-113) | I/O ports | Wired |
| 114 | [`irq_handler_get`](#irq_handler_get-kernel-gate-selector-114) | Interrupts | Wired |
| 115 | [`irq_handler_set_notification`](#irq_handler_set_notification-kernel-gate-selector-115) | Interrupts | Wired |
| 116 | [`irq_handler_ack`](#irq_handler_ack-kernel-gate-selector-116) | Interrupts | Wired |
| 117 | [`irq_handler_clear`](#irq_handler_clear-kernel-gate-selector-117) | Interrupts | Wired |
| 118 | [`irq_control_revoke`](#irq_control_revoke-kernel-gate-selector-118) | Interrupts | Wired |
| 120 | [`ring_create`](#ring_create-kernel-gate-selector-120) | Submission and completion rings | Wired |
| 121 | [`ring_enter`](#ring_enter-kernel-gate-selector-121) | Submission and completion rings | Wired |
| 128 | [`vcpu_bind`](#vcpu_bind-kernel-gate-selector-128) | Virtual machines (VCPU and EPT/NPT) | Wired |
| 129 | [`vcpu_inject`](#vcpu_inject-kernel-gate-selector-129) | Virtual machines (VCPU and EPT/NPT) | Wired |
| 130 | [`vcpu_inject_event`](#vcpu_inject_event-kernel-gate-selector-130) | Virtual machines (VCPU and EPT/NPT) | Wired |
| 131 | [`vcpu_pending`](#vcpu_pending-kernel-gate-selector-131) | Virtual machines (VCPU and EPT/NPT) | Wired |
| 132 | [`ept_map_frame`](#ept_map_frame-kernel-gate-selector-132) | Virtual machines (VCPU and EPT/NPT) | Wired |
| 133 | [`ept_destroy`](#ept_destroy-kernel-gate-selector-133) | Virtual machines (VCPU and EPT/NPT) | Wired |
| 134 | [`vcpu_inject_virq`](#vcpu_inject_virq-kernel-gate-selector-134) | Virtual machines (VCPU and EPT/NPT) | Wired |
| 136 | [`ept_retype`](#ept_retype-kernel-gate-selector-136) | Virtual machines (VCPU and EPT/NPT) | Wired |
| 140 | [`task_admit`](#task_admit-kernel-gate-selector-140) | Task control | Wired |
| 141 | [`task_set_cspace`](#task_set_cspace-kernel-gate-selector-141) | Task control | Wired |
| 142 | [`task_set_vspace`](#task_set_vspace-kernel-gate-selector-142) | Address spaces, frames and caches | Wired |
| 143 | [`vspace_map_frame`](#vspace_map_frame-kernel-gate-selector-143) | Address spaces, frames and caches | Wired |
| 144 | [`tcb_set_ipc_buffer`](#tcb_set_ipc_buffer-kernel-gate-selector-144) | Task control | Wired |
| 145 | [`tcb_bind_vspace`](#tcb_bind_vspace-kernel-gate-selector-145) | Task control | Wired |
| 146 | [`frame_freeze`](#frame_freeze-kernel-gate-selector-146) | Address spaces, frames and caches | Wired |
| 147 | [`vspace_map_exec`](#vspace_map_exec-kernel-gate-selector-147) | Address spaces, frames and caches | Wired |
| 148 | [`vspace_donate_table`](#vspace_donate_table-kernel-gate-selector-148) | Address spaces, frames and caches | Wired |
| 150 | [`driver_report`](#driver_report-kernel-gate-selector-150) | Device report slots | Wired |
| 151 | [`driver_report_read`](#driver_report_read-kernel-gate-selector-151) | Device report slots | Wired |

## Basic application calls

The typed application calls: give up the CPU, send and receive one word, map and unmap memory, ask the key vault for an operation, and exit.

### `yield` (kernel gate, selector 0)

**Status: wired.**

**What it does.** Gives up the rest of the caller's time slice. The caller stays runnable and is dispatched again by the scheduler.

**Why it exists.** Cooperative scheduling: a task that has nothing to do should not burn its budget.

**Capability presented in `rdi` (rights required).** none

Parameters: none.

**Result.** 0

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::yield_now(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `send` (kernel gate, selector 1)

**Status: wired.**

**What it does.** Sends one machine word through an endpoint capability. It never blocks: if the receiver is not waiting the word is queued.

**Why it exists.** The simplest one-way message, for events and hints where no answer is needed.

**Capability presented in `rdi` (rights required).** endpoint WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `msg` | message word |

**Result.** status; Ok(0)

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::send(&mut t, cap, msg);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `recv` (kernel gate, selector 2)

**Status: wired.**

**What it does.** Receives one word from an endpoint the caller holds READ on. With nothing queued it returns WouldBlock; a message that carries capabilities is not readable this way.

**Why it exists.** The word-only counterpart of send. Tasks that exchange capabilities use receive (37) instead.

**Capability presented in `rdi` (rights required).** endpoint READ

Parameters: none.

**Result.** status; Ok(word) | WouldBlock | Bounced(word)

**Statuses named for this call:** `WOULD_BLOCK`, `BOUNCED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::recv(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `map_memory` (kernel gate, selector 3)

**Status: wired.**

**What it does.** Maps the memory named by a memory capability into the caller's address space at a virtual address, for a length, with flags.

**Why it exists.** Gives a task access to memory it was granted, only as far as its MAP right allows.

**Capability presented in `rdi` (rights required).** memory MAP

| register | parameter | type |
|---|---|---|
| `rsi` | `vaddr` | integer |
| `rdx` | `len` | length or count |
| `r10` | `flags` | integer |

**Result.** status

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::map_memory(&mut t, cap, vaddr, len, flags);
// r = [rax, rdx]: rax is the status word, rdx the value
```

**Notes.** The rights of the capability bound what can be granted, whatever flags are requested.

### `unmap` (kernel gate, selector 4)

**Status: wired.**

**What it does.** Removes a mapping of memory the caller previously mapped.

**Why it exists.** Releases address space and revokes the caller's own access.

**Capability presented in `rdi` (rights required).** memory MAP

| register | parameter | type |
|---|---|---|
| `rsi` | `vaddr` | integer |
| `rdx` | `len` | length or count |

**Result.** status

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::unmap(&mut t, cap, vaddr, len);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `vault_op` (kernel gate, selector 5)

**Status: wired.**

**What it does.** Asks the key vault to perform one operation (for example a signature) using four message words. The vault authenticates the request against the caller's identity and the key never leaves the kernel.

**Why it exists.** Lets a program use a key it must never be able to read. Only the result of the operation crosses the boundary.

**Capability presented in `rdi` (rights required).** vault SIGN

| register | parameter | type |
|---|---|---|
| `rsi` | `word0` | message word |
| `rdx` | `word1` | message word |
| `r10` | `word2` | message word |
| `r8` | `word3` | message word |

**Result.** status; Ok(the number of operations the VAULT has performed for the caller, this one included). The kernel's VaultOp takes FOUR WORDS and no user pointer: nothing crosses the boundary by reference. The kernel binds the caller's id and the four words into one 40-byte request and the key vault authenticates it; the evidence stays in the kernel (never key extraction). DENIED when the vault refuses or the caller holds no grant over the key (the counter does not move); INVALID when no vault is attached or it holds no such key

**Statuses named for this call:** `DENIED`, `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::vault_op(&mut t, cap, word0, word1, word2, word3);
// r = [rax, rdx]: rax is the status word, rdx the value
```

**Notes.** The four words are bound together with the caller's identity into one request the vault authenticates. The call returns how many operations the vault has done for this caller. It never returns key material.

### `exit` (kernel gate, selector 6)

**Status: wired.**

**What it does.** Ends the calling task. The call does not return.

**Why it exists.** The orderly way for a task to finish.

**Capability presented in `rdi` (rights required).** none

| register | parameter | type |
|---|---|---|
| `rsi` | `status` | integer |

**Result.** does not return

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::exit(&mut t, cap, status);
// r = [rax, rdx]: rax is the status word, rdx the value
```

## Messages, calls and replies

Synchronous call and reply, asynchronous send, receive, and capability transfer. A message is one word of payload plus up to three capabilities.

### `call` (kernel gate, selector 34)

**Status: wired.**

**What it does.** Sends a message to a server through an endpoint capability and blocks the caller until the server answers with reply. The caller may carry up to three capabilities (this needs the GRANT right on the endpoint capability) and may name the slots in which capabilities of the reply will be installed.

**Why it exists.** Remote procedure call. A client gives the server exactly the authority the request needs and gets the answer in one step.

**Capability presented in `rdi` (rights required).** endpoint WRITE at rdi (+GRANT to carry capabilities)

| register | parameter | type |
|---|---|---|
| `rsi` | `msg` | message word |
| `rdx` | `caps_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |
| `r10` | `caps_len` | length or count |
| `r8` | `reply_slots` | message word |

**Result.** status; Ok(0) and the caller blocks until the reply. reply_slots = (receive CNode cptr << 16) | first slot index: where the capabilities the REPLY carries are installed when this call resumes (cptr resolved at depth r9 in the caller's CSpace, a Memory capability with WRITE for a CNode; capability i goes to slot first+i, occupied or out-of-range slots and revoked sources drop that capability, never the answer; 0 names none and the reply's capabilities are dropped). caps_ptr names caps_len little-endian words (at most 3, MAX_XFER_CAPS; more is INVALID), each a cptr, at the same depth r9, of a NON-EMPTY slot of the caller's own CSpace: the capabilities travel with the call and are installed in the receiver's slots at ITS receive (see receive). Carrying any needs GRANT on the endpoint capability (DENIED without; nothing queued). A carried cptr that resolves nowhere is LOOKUP and nothing is queued. caps_len 0 carries nothing (caps_ptr is not read)

**Statuses named for this call:** `DENIED`, `INVALID`, `LOOKUP`, `EMPTY`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
// Client: ask the server a question and wait for the answer
let r = kernel::call(&mut t, server_ep, /*msg*/ 42, /*caps_ptr*/ 0, /*caps_len*/ 0, /*reply_slots*/ 0);
if r[0] == status::OK { let answer = r[1]; }
```

**Notes.** While blocked in a call the caller is woken only by the answer. If the server dies without answering, the caller wakes with an aborted result. More than three capabilities is `INVALID`.

### `reply` (kernel gate, selector 35)

**Status: wired.**

**What it does.** Answers the call (or fault) the replier's last receive took, using the single-use reply capability the kernel keeps in the replier's reply slot. The reply may carry capabilities if the call could.

**Why it exists.** Completes a call. The reply capability is one-shot, so a server can never answer a client twice, or answer a client that was not waiting.

**Capability presented in `rdi` (rights required).** the reply capability in the caller's reply slot (kept by the kernel; never a pointer)

| register | parameter | type |
|---|---|---|
| `rsi` | `msg` | message word |
| `rdx` | `caps_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |
| `r10` | `caps_len` | length or count |

**Result.** status; Ok(0), single use. Answers the call (or fault) the caller's last receive took: the slot is consumed on success and kept on a refusal; INVALID with nothing pending; caps_ptr names caps_len words (at most 3; more is INVALID), each a cptr at depth r9 of a NON-EMPTY slot of the REPLIER's own CSpace; they are installed in the slots the caller named in call.reply_slots when it resumes. Carrying any needs GRANT on the reply capability (inherited from the call's endpoint capability): without it the reply is DENIED and NOTHING is consumed (the slot is kept, the caller stays blocked). A carried cptr that resolves nowhere is LOOKUP, also consuming nothing. A reply to a fault carries nothing

**Statuses named for this call:** `DENIED`, `INVALID`, `LOOKUP`, `EMPTY`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::reply(&mut t, cap, msg, caps_ptr, caps_len);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `send_caps` (kernel gate, selector 36)

**Status: wired.**

**What it does.** Like send, but may carry up to three capabilities, snapshotted at the moment of sending. Needs GRANT on the endpoint capability to carry any. Never blocks.

**Why it exists.** Delegation without a round trip: hand a service a capability together with a message.

**Capability presented in `rdi` (rights required).** endpoint WRITE at rdi (+GRANT to carry capabilities)

| register | parameter | type |
|---|---|---|
| `rsi` | `msg` | message word |
| `rdx` | `caps_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |
| `r10` | `caps_len` | length or count |

**Result.** status; Ok(0), never blocks. As call: caps_ptr names caps_len cptrs (at most 3) of capabilities in the sender's own CSpace, snapshotted AT SEND and installed at the receiver's receive; GRANT is needed to carry any (DENIED without, nothing queued); caps_len 0 is a plain send

**Statuses named for this call:** `DENIED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::send_caps(&mut t, cap, msg, caps_ptr, caps_len);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `receive` (kernel gate, selector 37)

**Status: wired.**

**What it does.** Receives the next message on the caller's own endpoint. Writes a seven-word record (kind, sender, message word, badge, capabilities installed, task, capabilities sent) at out_ptr and returns the kind. A call, badged call or fault leaves a one-shot reply capability in the reply slot. Capabilities carried by the message are installed in the CNode slots the caller names.

**Why it exists.** The server loop. One entry point distinguishes plain messages, calls, signals from a bound notification, and faults.

**Capability presented in `rdi` (rights required).** endpoint READ at rdi, the caller's OWN endpoint

| register | parameter | type |
|---|---|---|
| `rsi` | `out_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |
| `rdx` | `recv_cnode_cptr` | integer |
| `r10` | `first_index` | integer |

**Result.** status OK; value = the kind (0 Empty, 1 Msg, 2 Call, 3 Bounced, 4 Signal, 5 BadgedMsg, 6 BadgedCall, 7 Fault); seven words are written at out_ptr: {kind, from, msg, badge, ncaps_installed, task, ncaps_sent}. A Call, BadgedCall or Fault stores its one-shot reply capability in the caller's reply slot (ONE per task: a receive while it is occupied is INVALID and consumes nothing). The capabilities the message carried are installed in slots first_index, first_index+1,.. of the CNode named by the Memory capability (WRITE) at recv_cnode_cptr (same depth; 0 = none named: nothing installed): each copy is a CHILD of the sender's capability (revoking it revokes the copy) with the rights the sender held when it sent. A capability is NOT delivered when its slot is out of range or occupied (nothing is overwritten), when the sender's capability was deleted or revoked since, or when recv_cnode_cptr is not a usable CNode capability: the message is delivered all the same, ncaps_installed counts what was installed and ncaps_sent what the sender sent. An out_ptr the caller cannot write is INVALID and consumes nothing

**Statuses named for this call:** `OK`, `INVALID`, `BOUNCED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
// Server loop: take the next message into a seven-word record
let mut rec = [0u64; 7];
let r = kernel::receive(&mut t, my_ep, rec.as_mut_ptr() as u64, /*recv_cnode*/ 0, /*first_index*/ 0);
// r[1] is the kind: 2 = call (a reply capability now waits in the reply slot), 1 = message, 7 = fault ...
```

**Notes.** Kinds returned in the value: 0 empty, 1 message, 2 call, 3 bounced, 4 signal (from a bound notification), 5 badged message, 6 badged call, 7 fault. The reply slot holds one reply capability per task: a receive while it is occupied is `INVALID`.

### `reply_recv` (kernel gate, selector 54)

**Status: wired.**

**What it does.** Answers the pending call and receives the next message in a single step. Both halves are checked before either happens.

**Why it exists.** A server's main loop in one kernel entry instead of two: lower overhead and no window between answering and listening.

**Capability presented in `rdi` (rights required).** endpoint READ at rdi, the caller's OWN endpoint (and the reply slot)

| register | parameter | type |
|---|---|---|
| `rsi` | `msg` | message word |
| `rdx` | `out_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |

**Result.** status; answers the pending call, then receives on the endpoint in one step: the Received record is written at out_ptr as for receive, value = its kind. Both halves are checked before either happens: no pending reply, an unwritable out_ptr or a non-own endpoint is refused and changes nothing

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
// Server loop in one entry: answer the last call and wait for the next message
let r = kernel::reply_recv(&mut t, my_ep, answer, rec.as_mut_ptr() as u64);
```

### `slot_send` (kernel gate, selector 68)

**Status: wired.**

**What it does.** Sends a word through the endpoint capability held in a named slot of a CNode, carrying that capability's badge.

**Why it exists.** Sending by slot lets a holder use a capability without moving it, and the receiver learns which badge (which client) the word came through.

**Capability presented in `rdi` (rights required).** cnode READ

| register | parameter | type |
|---|---|---|
| `rsi` | `idx` | integer |
| `rdx` | `msg` | message word |

**Result.** status; sends through the ENDPOINT capability (WRITE) held in the slot, carrying that capability's badge

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::slot_send(&mut t, cap, idx, msg);
// r = [rax, rdx]: rax is the status word, rdx the value
```

## Notifications

A notification is a word of badge bits that a signaller sets and a waiter collects: the lightweight event channel.

### `signal` (kernel gate, selector 38)

**Status: wired.**

**What it does.** ORs the capability's badge into the notification word, wakes the first waiter and the task the notification is bound to. Never blocks.

**Why it exists.** The event channel for interrupts and wake-ups: the signaller cannot choose the bits, only the badge the capability was minted with.

**Capability presented in `rdi` (rights required).** signal WRITE

Parameters: none.

**Result.** status; Ok(0)

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
// A driver signals the notification it holds a signal capability for; the waiter sees the badge bits
let r = kernel::signal(&mut t, sig_cap);
```

### `wait` (kernel gate, selector 39)

**Status: wired.**

**What it does.** Returns the pending badge word and clears it. With nothing pending the caller blocks until a signal arrives and then receives the word.

**Why it exists.** Collects events. The word is never 0, so a returned word always means something happened.

**Capability presented in `rdi` (rights required).** notification READ at rdi

Parameters: none.

**Result.** status; value = the badge word (never 0). A pending word is returned at once and clears; with none the caller BLOCKS (Ok(0) and it is not resumed) until a signal wakes it, which hands it the word as the resume value. DENIED for a capability that is not a notification with READ (nothing blocks); LOOKUP for an empty slot; INVALID for a task that is already held or a notification that no longer exists. A wait whose notification is destroyed resumes INVALID

**Statuses named for this call:** `DENIED`, `INVALID`, `LOOKUP`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
// Collect events: blocks until some badge bit is set, then returns the word
let r = kernel::wait(&mut t, wait_cap);
let bits = r[1];   // never 0
```

### `bind_notification` (kernel gate, selector 40)

**Status: wired.**

**What it does.** Binds a task and a notification to each other, so that the task's receive returns the notification's pending word ahead of queued messages.

**Why it exists.** One loop can serve both messages and events: a server waits on a single endpoint and still sees interrupts.

**Capability presented in `rdi` (rights required).** task WRITE at rdi

| register | parameter | type |
|---|---|---|
| `rsi` | `wait_cptr` | message word |

**Result.** status; wait_cptr is a cptr (same depth r9) of a notification capability with READ in the caller's own CSpace; the task and the notification are bound to each other (a bound task's receive returns the notification's pending word first). INVALID when either side is already bound, DENIED for a capability of the wrong kind or without the right

**Statuses named for this call:** `DENIED`, `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::bind_notification(&mut t, cap, wait_cptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `unbind_notification` (kernel gate, selector 41)

**Status: wired.**

**What it does.** Releases the task's bound notification so it may be bound again elsewhere.

**Why it exists.** Allows the binding to be handed to another task.

**Capability presented in `rdi` (rights required).** task WRITE at rdi

Parameters: none.

**Result.** status; releases the task's bound notification (it may then be bound again). INVALID when nothing is bound

**Statuses named for this call:** `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::unbind_notification(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

## Faults and restart

What happens when a task faults or runs out of budget, and how a supervisor answers.

### `set_fault_handler` (kernel gate, selector 44)

**Status: wired.**

**What it does.** Registers an endpoint (WRITE) as the place the target task's faults are delivered. A later registration replaces an earlier one.

**Why it exists.** Supervision: a fault becomes a message to a program that can decide what to do, instead of an automatic kill.

**Capability presented in `rdi` (rights required).** task WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `handler_cptr` | integer |

**Result.** status; the handler is an Endpoint capability with WRITE at handler_cptr in the caller's CSpace (same depth as rdi); a READ-only endpoint or a capability of another kind is DENIED

**Statuses named for this call:** `DENIED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::set_fault_handler(&mut t, cap, handler_cptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `clear_fault_handler` (kernel gate, selector 45)

**Status: wired.**

**What it does.** Removes the target task's fault handler.

**Why it exists.** With no handler a faulting task is killed; clearing returns the task to that policy.

**Capability presented in `rdi` (rights required).** task WRITE

Parameters: none.

**Result.** status

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::clear_fault_handler(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `restart_task` (kernel gate, selector 46)

**Status: wired.**

**What it does.** Ends a fault suspension without a reply: the task runs again, the fault's reply capability dies, and the restart is counted.

**Why it exists.** A supervisor's way to restart a faulted task.

**Capability presented in `rdi` (rights required).** task WRITE

Parameters: none.

**Result.** status

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::restart_task(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `set_timeout_handler` (kernel gate, selector 102)

**Status: wired.**

**What it does.** Registers an endpoint that receives a timeout fault when the task's scheduling-context budget runs out. The task is suspended until the handler replies or restarts it.

**Why it exists.** Budget enforcement as a message: a supervisor can notice an overrunning task, not only throttle it.

**Capability presented in `rdi` (rights required).** task WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `handler_cptr` | integer |

**Result.** status; the handler's endpoint (WRITE) at handler_cptr receives this task's TIMEOUT faults (fault code 8: addr = the scheduling context, ip = its budget) when its budget runs out; the task is suspended until the handler replies or restarts it; a task cannot be its own handler (INVALID)

**Statuses named for this call:** `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::set_timeout_handler(&mut t, cap, handler_cptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `clear_timeout_handler` (kernel gate, selector 103)

**Status: wired.**

**What it does.** Removes the timeout handler. An exhausted budget then only throttles the task.

**Why it exists.** Returns to throttle-only behaviour.

**Capability presented in `rdi` (rights required).** task WRITE

Parameters: none.

**Result.** status; an exhausted budget then only throttles the task; INVALID when none is set

**Statuses named for this call:** `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::clear_timeout_handler(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

## Priorities and partitions

Strict priorities, the maximum controlled priority, and fixed partition windows.

### `set_partition_schedule` (kernel gate, selector 32)

**Status: wired.**

**What it does.** Installs a fixed cyclic schedule of partition windows read from the caller's memory (pairs of partition number and ticks).

**Why it exists.** Time partitioning: one partition's CPU time does not depend on what another does.

**Capability presented in `rdi` (rights required).** schedule WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `windows_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |
| `rdx` | `count` | length or count |

**Result.** status; Ok(windows). count windows of two u64 words {partition, ticks} are read from windows_ptr; count 0 or above the kernel's window limit, a pointer outside the caller's memory, a zero-tick window or partition 0 are INVALID

**Statuses named for this call:** `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::set_partition_schedule(&mut t, cap, windows_ptr, count);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `reset_partition_schedule` (kernel gate, selector 33)

**Status: wired.**

**What it does.** Removes the installed partition schedule; scheduling returns to the ordinary two-tier scheduler. Returns how many windows were removed.

**Why it exists.** Allows a system to leave partitioned mode.

**Capability presented in `rdi` (rights required).** schedule WRITE

Parameters: none.

**Result.** status; Ok(windows removed)

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::reset_partition_schedule(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `move_task_partition` (kernel gate, selector 47)

**Status: wired.**

**What it does.** Moves a task to another scheduling partition.

**Why it exists.** Lets the authority holder re-classify a task, for example when its trust level changes.

**Capability presented in `rdi` (rights required).** schedule WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `task` | integer |
| `rdx` | `partition` | integer |

**Result.** status

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::move_task_partition(&mut t, cap, task, partition);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `set_task_priority` (kernel gate, selector 56)

**Status: wired.**

**What it does.** Sets a task's priority (0 to 255), bounded by the maximum controlled priority of the task whose authority is used.

**Why it exists.** Strict priority scheduling with a safe delegation rule: a task cannot raise anyone above its own limit.

**Capability presented in `rdi` (rights required).** task WRITE (the TARGET; the caller is the authority)

| register | parameter | type |
|---|---|---|
| `rsi` | `prio` | integer |

**Result.** status; priorities 0..=255, bounded by the authority's maximum controlled priority

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::set_task_priority(&mut t, cap, prio);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `set_task_mcp` (kernel gate, selector 57)

**Status: wired.**

**What it does.** Sets the target's maximum controlled priority, never above the caller's own.

**Why it exists.** Bounds how high a delegated scheduler may set priorities.

**Capability presented in `rdi` (rights required).** task WRITE (the TARGET; the caller is the authority)

| register | parameter | type |
|---|---|---|
| `rsi` | `mcp` | integer |

**Result.** status; sets the target's maximum controlled priority, never above the caller's own

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::set_task_mcp(&mut t, cap, mcp);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `set_task_mcp_sealed` (kernel gate, selector 58)

**Status: wired.**

**What it does.** Sets a task's maximum controlled priority using the schedule authority.

**Why it exists.** The authority-holder form of set_task_mcp, used by a root task.

**Capability presented in `rdi` (rights required).** schedule WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `task` | integer |
| `rdx` | `mcp` | integer |

**Result.** status

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::set_task_mcp_sealed(&mut t, cap, task, mcp);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `clear_task_priority_sealed` (kernel gate, selector 59)

**Status: wired.**

**What it does.** Clears a task's explicit priority so it returns to the weight-based tier.

**Why it exists.** Undoes a priority assignment.

**Capability presented in `rdi` (rights required).** schedule WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `task` | integer |

**Result.** status

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::clear_task_priority_sealed(&mut t, cap, task);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `set_task_passive` (kernel gate, selector 99)

**Status: wired.**

**What it does.** Marks a task passive (1) or active (0). A passive task has no time of its own and runs only while it holds a scheduling context donated by a caller.

**Why it exists.** Passive servers: a server costs CPU only when a client is calling it, and that time is charged to the client.

**Capability presented in `rdi` (rights required).** schedule WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `task` | integer |
| `rdx` | `passive` | integer |

**Result.** status; passive is 0 or 1 (anything else INVALID); Ok(1) when the task was passive before, Ok(0) when it was not. A passive task has no time of its own: it is dispatched only while it runs on a scheduling context, which a call donates (and which returns when the call is answered or aborted)

**Statuses named for this call:** `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::set_task_passive(&mut t, cap, task, passive);
// r = [rax, rdx]: rax is the status word, rdx the value
```

## Scheduling contexts

Budgets, periods, donation, and passive servers (the mixed-criticality scheduling model).

### `create_sched_context` (kernel gate, selector 51)

**Status: wired.**

**What it does.** Creates a scheduling-context capability with a budget and period in a named CNode slot. Under sealed object creation the context is created unconfigured (budget 0, period 0) and must be configured with configure_sched_context.

**Why it exists.** A scheduling context is the right to a share of CPU time, separate from the thread that uses it.

**Capability presented in `rdi` (rights required).** untyped WRITE (rdi)

| register | parameter | type |
|---|---|---|
| `rsi` | `budget` | integer |
| `rdx` | `period` | integer |
| `r10` | `dest_cptr` | integer |
| `r8` | `dest_index` | integer |

**Result.** status; Ok(1). ABI 2.0: the scheduling-context capability is installed in slot dest_index of the CNode at dest_cptr (the retype convention), never written out as bytes; 0 < budget <= period, else INVALID; an occupied slot is ALREADY_MAPPED

**Statuses named for this call:** `INVALID`, `ALREADY_MAPPED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::create_sched_context(&mut t, cap, budget, period, dest_cptr, dest_index);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `bind_sched_context` (kernel gate, selector 52)

**Status: wired.**

**What it does.** Binds a scheduling context to a task.

**Why it exists.** A task with a bound context is scheduled on that context's budget.

**Capability presented in `rdi` (rights required).** sched-context WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `task_cptr` | integer |

**Result.** status; the Task capability (WRITE) is at task_cptr in the caller's CSpace, same depth as rdi

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::bind_sched_context(&mut t, cap, task_cptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `unbind_sched_context` (kernel gate, selector 53)

**Status: wired.**

**What it does.** Unbinds a scheduling context from its task.

**Why it exists.** Returns the context to its owner and stops the task being dispatched on it.

**Capability presented in `rdi` (rights required).** sched-context WRITE

Parameters: none.

**Result.** status

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::unbind_sched_context(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `bind_sched_context_notification` (kernel gate, selector 100)

**Status: wired.**

**What it does.** Binds a scheduling context to a notification, so a passive task woken by a signal on it runs on that context until it waits again.

**Why it exists.** Interrupt-driven passive servers run on a budget someone chose and pays for.

**Capability presented in `rdi` (rights required).** schedule WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `sched_context` | integer |
| `rdx` | `notification` | integer |

**Result.** status; the context must have no owner, no holder and no notification, and the notification must exist and have no context (else INVALID). A passive task woken by a signal on the notification (as a waiter, or as the task it is bound to) then runs on, is charged to and is throttled by the context until it waits again, or exits, or the notification is destroyed

**Statuses named for this call:** `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::bind_sched_context_notification(&mut t, cap, sched_context, notification);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `configure_sched_context` (kernel gate, selector 101)

**Status: wired.**

**What it does.** Sets budget and period of a scheduling context and the core it may run on. Needs the schedule authority.

**Why it exists.** Only the authority holder can hand out CPU time; a context created by retype is useless until it is configured.

**Capability presented in `rdi` (rights required).** schedule WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `sc_cptr` | integer |
| `rdx` | `budget` | integer |
| `r10` | `period` | integer |
| `r8` | `core` | integer |

**Result.** status; per-core scheduling control: the scheduling context (WRITE) at sc_cptr in the caller's CSpace gets budget and period, its refills restart full, and it runs only on core (per-core SchedControl; a core the kernel does not schedule is INVALID); 0 < budget <= period, else INVALID; without the schedule authority at rdi DENIED. Once object creation is sealed a retyped context is UNCONFIGURED (create_sched_context with budget 0 and period 0; any other budget is DENIED) and this is the only way it gets CPU time

**Statuses named for this call:** `DENIED`, `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::configure_sched_context(&mut t, cap, sc_cptr, budget, period, core);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `sched_context_consumed` (kernel gate, selector 104)

**Status: wired.**

**What it does.** Returns the dispatches charged to the context since the last read, and resets the count.

**Why it exists.** Accounting: lets a supervisor see how much of its budget a task used.

**Capability presented in `rdi` (rights required).** sched-context WRITE

Parameters: none.

**Result.** status; Ok(ticks): the dispatches charged to the scheduling context at rdi since its consumed time was last read; the count resets

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::sched_context_consumed(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `sched_context_yield_to` (kernel gate, selector 105)

**Status: wired.**

**What it does.** Makes the task running on the context run first among tasks of its priority at the next scheduling decision, and yields the caller. Never ahead of a higher priority.

**Why it exists.** Directed hand-off between cooperating tasks.

**Capability presented in `rdi` (rights required).** sched-context WRITE

Parameters: none.

**Result.** status; the task running on the scheduling context at rdi runs first among the tasks at its priority at the next priority-tier decision, and the caller yields; the task must be in the priority tier (else INVALID), in the caller's scheduling partition and at most the caller's MCP (else DENIED); never ahead of a higher priority

**Statuses named for this call:** `DENIED`, `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::sched_context_yield_to(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

## Untyped memory and retype

How every kernel object is made: by carving it out of untyped memory the caller already owns.

### `retype` (kernel gate, selector 48)

**Status: wired.**

**What it does.** Carves new kernel objects out of an untyped capability and installs their capabilities in consecutive slots of a destination CNode. Objects: frame, nested untyped, CNode, large frames (2 MiB, 1 GiB), ASID control, task, notification, VCPU (and, reserved, EPT). The memory is charged to the untyped capability.

**Why it exists.** The only way kernel objects come to exist. The kernel never allocates for a caller on its own, so a program cannot make the kernel run out of memory for other programs.

**Capability presented in `rdi` (rights required).** untyped WRITE (rdi); the destination CNode is a Memory capability with WRITE named by dest_cptr

| register | parameter | type |
|---|---|---|
| `rsi` | `kind` | integer |
| `rdx` | `count` | length or count |
| `r10` | `dest_cptr` | integer |
| `r8` | `dest_index` | integer |

**Result.** status; Ok(count). The new capabilities are installed in the destination CNode's slots dest_index.. dest_index+count (all empty, or nothing is made); kind: 0 = Frame, n = Untyped{frames:n}, RETYPE_KIND_CNODE_FLAG | bits = CNode{bits}, RETYPE_KIND_LARGE_FRAME_FLAG | 1 = Frame2M and | 2 = Frame1G, RETYPE_KIND_ASID_POOL_FLAG | pcids = AsidPool{pcids}, RETYPE_KIND_TASK_FLAG = Task, RETYPE_KIND_NOTIFICATION_FLAG = Notification; any other kind is INVALID. ABI 2.0: dest_cptr is resolved at the same depth (r9) as rdi

**Statuses named for this call:** `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
// Carve one 4 KiB frame and one notification out of an untyped capability `ut`, into slots 10 and 11 of CNode `cn`
let r = kernel::retype(&mut t, ut, 0, 1, cn, 10);                          // kind 0 = frame
assert_eq!(r[0], status::OK);
let r = kernel::retype(&mut t, ut, RETYPE_KIND_NOTIFICATION_FLAG, 1, cn, 11);
assert_eq!(r[0], status::OK);
```

**Notes.** The `RETYPE_KIND_ASID_POOL_FLAG` kind is refused with `INVALID`: a pool owns a range of the 4,095 PCIDs that no other pool may share, so pools are made with `asid_pool_make`. `RETYPE_KIND_EPT_FLAG` answers `INVALID` today. A VCPU made by retype arrives as a Task-kind capability with READ and WRITE. The result value is the number of objects made; if any destination slot is not empty, nothing is made.

### `revoke_untyped_step` (kernel gate, selector 49)

**Status: superseded.** Replaced by selector 66. The row is kept forever (the table is append-only) but the kernel never serves it: it answers `NOT_WIRED`.

**What it does.** Superseded by cnode_revoke_step. Never served; answers NOT_WIRED.

**Why it exists.** Kept because the table is append-only.

**Capability presented in `rdi` (rights required).** untyped WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `budget` | length or count |

**Result.** SUPERSEDED by 66 cnode_revoke_step; never served, answers NOT_WIRED. Was: status; Ok(objects revoked in this step); call again until done

**Statuses named for this call:** `NOT_WIRED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::revoke_untyped_step(&mut t, cap, budget);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `destroy_root_untyped` (kernel gate, selector 50)

**Status: wired.**

**What it does.** Destroys every object made from the root untyped capability, returning the count.

**Why it exists.** Tears down a whole subsystem and recovers its memory in one operation.

**Capability presented in `rdi` (rights required).** untyped WRITE

Parameters: none.

**Result.** status; Ok(objects destroyed)

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::destroy_root_untyped(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

## CNodes and capability slots

Copying, minting, moving, deleting and revoking capabilities, and reading a slot.

### `mint_badged_endpoint` (kernel gate, selector 55)

**Status: superseded.** Replaced by selector 62. The row is kept forever (the table is append-only) but the kernel never serves it: it answers `NOT_WIRED`.

**What it does.** Superseded by cnode_mint. Never served; answers NOT_WIRED.

**Why it exists.** Kept because the table is append-only.

**Capability presented in `rdi` (rights required).** none

| register | parameter | type |
|---|---|---|
| `rsi` | `target` | integer |
| `rdx` | `badge` | integer |
| `r10` | `rights` | integer |
| `r8` | `out_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |

**Result.** SUPERSEDED by 62 cnode_mint; never served, answers NOT_WIRED. Was: status; the badged endpoint capability is written at out_ptr (the kernel checks the caller may mint)

**Statuses named for this call:** `NOT_WIRED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::mint_badged_endpoint(&mut t, cap, target, badge, rights, out_ptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `cnode_install_initial` (kernel gate, selector 60)

**Status: wired.**

**What it does.** Installs a root-of-derivation capability (kind, object, rights, badge read from a four-word record) in a CNode slot. Refused for the reserved authorities.

**Why it exists.** Used only by the boot authority to populate the first tasks' CNodes.

**Capability presented in `rdi` (rights required).** schedule WRITE (the boot authority)

| register | parameter | type |
|---|---|---|
| `rsi` | `cnode_cptr` | integer |
| `rdx` | `idx` | integer |
| `r10` | `spec_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |

**Result.** status; the CNode (Memory, WRITE) is at cnode_cptr in the caller's CSpace, same depth as rdi; an object that is a reserved authority (schedule, spawn, sandbox, exec-map, IOMMU, hypervisor) is DENIED; spec = {kind (capability kind value), object, rights, badge}: installs a root-of-derivation capability in the CNode slot (needs WRITE on the CNode)

**Statuses named for this call:** `DENIED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::cnode_install_initial(&mut t, cap, cnode_cptr, idx, spec_ptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `cnode_copy` (kernel gate, selector 61)

**Status: wired.**

**What it does.** Copies a capability into an empty slot as a child of the source, with rights reduced by a mask.

**Why it exists.** Delegation with attenuation: pass on less than you hold, never more.

**Capability presented in `rdi` (rights required).** cnode WRITE (the destination slot, an EMPTY slot named by rdi)

| register | parameter | type |
|---|---|---|
| `rsi` | `src_cptr` | integer |
| `rdx` | `mask` | integer |

**Result.** status; the copy is a child of the source in the derivation tree, rights attenuated by mask (a mask with a bit no right claims is INVALID); an occupied destination is ALREADY_MAPPED; every slot is a cptr at depth r9

**Statuses named for this call:** `INVALID`, `ALREADY_MAPPED`, `EMPTY`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::cnode_copy(&mut t, cap, src_cptr, mask);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `cnode_mint` (kernel gate, selector 62)

**Status: wired.**

**What it does.** A copy that also stamps a badge (once) on an unbadged endpoint capability.

**Why it exists.** Badges let a server tell its clients apart without trusting what they say.

**Capability presented in `rdi` (rights required).** cnode WRITE (the destination slot, an EMPTY slot named by rdi)

| register | parameter | type |
|---|---|---|
| `rsi` | `src_cptr` | integer |
| `rdx` | `mask` | integer |
| `r10` | `badge` | integer |

**Result.** status; a copy that also stamps a badge, once, on an unbadged endpoint capability

**Statuses named for this call:** `EMPTY`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
// Give a client a badged copy of the server's endpoint capability (slot `src`) in the empty slot `dst` of the client's CNode
let r = kernel::cnode_mint(&mut t, dst, src, WRITE_ONLY_MASK, /*badge*/ 7);
assert_eq!(r[0], status::OK);
```

### `cnode_move` (kernel gate, selector 63)

**Status: wired.**

**What it does.** Moves a capability to an empty slot. The source slot becomes empty; the derivation tree is unchanged.

**Why it exists.** Relocate a capability without creating a second one.

**Capability presented in `rdi` (rights required).** cnode WRITE (the destination slot, an EMPTY slot named by rdi)

| register | parameter | type |
|---|---|---|
| `rsi` | `src_cptr` | integer |

**Result.** status; the source slot becomes empty, the derivation tree is unchanged

**Statuses named for this call:** `EMPTY`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::cnode_move(&mut t, cap, src_cptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `cnode_mutate` (kernel gate, selector 64)

**Status: wired.**

**What it does.** A move that also attenuates and, optionally, re-badges.

**Why it exists.** Move, narrow and badge in one atomic step.

**Capability presented in `rdi` (rights required).** cnode WRITE (the destination slot, an EMPTY slot named by rdi)

| register | parameter | type |
|---|---|---|
| `rsi` | `src_cptr` | integer |
| `rdx` | `mask` | integer |
| `r10` | `has_badge` | integer |
| `r8` | `badge` | integer |

**Result.** status; a move that attenuates and, when has_badge is 1, re-badges

**Statuses named for this call:** `EMPTY`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::cnode_mutate(&mut t, cap, src_cptr, mask, has_badge, badge);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `cnode_delete` (kernel gate, selector 65)

**Status: wired.**

**What it does.** Empties a slot.

**Why it exists.** Drop authority you no longer need.

**Capability presented in `rdi` (rights required).** cnode WRITE (the slot named by rdi)

Parameters: none.

**Result.** status; empties the slot

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::cnode_delete(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `cnode_revoke_step` (kernel gate, selector 66)

**Status: wired.**

**What it does.** Deletes descendants of a capability in budgeted steps and returns how many remain. Call again until it returns 0.

**Why it exists.** Revocation that can be preempted: a large revoke never stalls the kernel for an unbounded time.

**Capability presented in `rdi` (rights required).** cnode WRITE (the slot named by rdi)

| register | parameter | type |
|---|---|---|
| `rsi` | `budget` | length or count |

**Result.** status; Ok(remaining): the number of descendants still alive after this step, 0 = done; call again until 0 (preemptible, budgeted)

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
// Revoke a capability and everything derived from it, a bounded step at a time
loop {
    let r = kernel::cnode_revoke_step(&mut t, slot, /*budget*/ 16);
    if r[0] != status::OK || r[1] == 0 { break; }   // r[1] = descendants still alive
}
```

### `cnode_lookup` (kernel gate, selector 67)

**Status: wired.**

**What it does.** Reads a slot: writes five words (empty flag, kind, object, rights, badge) at out_ptr.

**Why it exists.** Lets a program inspect what it holds, without being able to forge anything.

**Capability presented in `rdi` (rights required).** cnode READ (the slot named by rdi)

| register | parameter | type |
|---|---|---|
| `rsi` | `out_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |

**Result.** status; five words are written at out_ptr: {empty (1 or 0), kind (capability kind value), object, rights, badge}; an empty slot answers OK with empty = 1

**Statuses named for this call:** `OK`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::cnode_lookup(&mut t, cap, out_ptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

## Task control

Suspend, resume, read and write registers, configure, admit and place tasks.

### `tcb_suspend` (kernel gate, selector 82)

**Status: wired.**

**What it does.** Suspends a task. Idempotent. It is not dispatched and no wake reaches it until resumed.

**Why it exists.** Debugging, migration and supervision.

**Capability presented in `rdi` (rights required).** task WRITE

Parameters: none.

**Result.** status; idempotent; the task is not dispatched and no wake reaches it until tcb_resume

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::tcb_suspend(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `tcb_resume` (kernel gate, selector 83)

**Status: wired.**

**What it does.** Ends a suspension. It does not end a block on a call, wait or fault.

**Why it exists.** Counterpart of suspend.

**Capability presented in `rdi` (rights required).** task WRITE

Parameters: none.

**Result.** status; INVALID when the task is not suspended; ends only the suspension (a task blocked on a call, wait or fault stays blocked on that)

**Statuses named for this call:** `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::tcb_resume(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `tcb_write_registers` (kernel gate, selector 84)

**Status: wired.**

**What it does.** Writes the 18 saved registers of a stopped task. Refused while the task runs and for privileged flag bits.

**Why it exists.** Set up a new task's entry state or repair a faulted one.

**Capability presented in `rdi` (rights required).** task WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `regs_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |

**Result.** status; 18 u64 words: rax rbx rcx rdx rsi rdi rbp rsp r8..r15 (0..15), rip (16), rflags (17); INVALID while the task is running, and for flags with IOPL/NT/VM/VIF/VIP set or bit 1 clear

**Statuses named for this call:** `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::tcb_write_registers(&mut t, cap, regs_ptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `tcb_read_registers` (kernel gate, selector 85)

**Status: wired.**

**What it does.** Reads the 18 saved registers of a stopped task into the caller's memory.

**Why it exists.** Inspect a task at a fault.

**Capability presented in `rdi` (rights required).** task READ

| register | parameter | type |
|---|---|---|
| `rsi` | `out_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |

**Result.** status; the same 18 words are written at out_ptr; INVALID while the task is running

**Statuses named for this call:** `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::tcb_read_registers(&mut t, cap, out_ptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `tcb_configure` (kernel gate, selector 86)

**Status: wired.**

**What it does.** Sets priority, maximum controlled priority, weight and fault handler in one all-or-nothing call.

**Why it exists.** One atomic configuration so a task is never half set up.

**Capability presented in `rdi` (rights required).** task WRITE (the TARGET; the caller is the authority)

| register | parameter | type |
|---|---|---|
| `rsi` | `config_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |

**Result.** status; the TARGET Task capability (WRITE) is presented at rdi and the CALLER is the authority (its own maximum controlled priority bounds priority and mcp); config = 5 u64 words: present-mask (bit0 priority, bit1 mcp, bit2 weight, bit3 fault handler), priority, mcp, weight, fault_handler_endpoint_cptr (an Endpoint capability with WRITE in the caller's CSpace, same depth as rdi); ALL-OR-NOTHING; DENIED above the authority's maximum controlled priority

**Statuses named for this call:** `DENIED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::tcb_configure(&mut t, cap, config_ptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `task_admit` (kernel gate, selector 140)

**Status: wired.**

**What it does.** Releases a freshly retyped task to run, in the kernel (0) or sandbox (1) partition.

**Why it exists.** A new task is born parked; admission is the explicit, authority-checked step that lets it run.

**Capability presented in `rdi` (rights required).** spawn authority WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `task` | integer |
| `rdx` | `confined` | integer |

**Result.** status; rdi is the spawn authority (Task capability on the spawn object, WRITE) and task a Task capability (WRITE) for a freshly retyped task: OK releases it. confined is 0 (kernel partition) or 1 (sandbox partition), else INVALID. A task that is not fresh, or a second admission, is INVALID; DENIED without the authority, with the wrong kind, or when releasing it unconfined would split the domains; a refused call changes nothing

**Statuses named for this call:** `OK`, `DENIED`, `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::task_admit(&mut t, cap, task, confined);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `task_set_cspace` (kernel gate, selector 141)

**Status: wired.**

**What it does.** Makes a CNode the root of a task's capability space.

**Why it exists.** A task can only name what is in its CSpace; this decides what that is.

**Capability presented in `rdi` (rights required).** schedule authority WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `task` | integer |
| `rdx` | `cnode` | integer |

**Result.** status; rdi is the schedule authority (Task capability on the schedule object, WRITE), task a Task capability (WRITE) and cnode a CNode (Memory) capability (READ), all cptrs at the caller's depth: the CNode becomes the root of that task's CSpace. DENIED without the authority or with a wrong kind or right; INVALID for a CNode that is gone or a task that does not exist; a refused call changes nothing

**Statuses named for this call:** `DENIED`, `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::task_set_cspace(&mut t, cap, task, cnode);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `tcb_set_ipc_buffer` (kernel gate, selector 144)

**Status: wired.**

**What it does.** Registers an IPC buffer address (512-byte aligned, inside a mapped range); 0 unbinds.

**Why it exists.** Gives a task a place for larger messages.

**Capability presented in `rdi` (rights required).** task WRITE (the TARGET)

| register | parameter | type |
|---|---|---|
| `rsi` | `vaddr` | integer |

**Result.** status; the target Task capability (WRITE) is at rdi in the caller's CSpace; vaddr must be 512-byte aligned and the whole buffer must lie inside one range the target has mapped, else INVALID; 0 unbinds; DENIED without WRITE or for a capability of another kind, INVALID for a task awaiting placement (no address space); a refused call changes nothing

**Statuses named for this call:** `DENIED`, `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::tcb_set_ipc_buffer(&mut t, cap, vaddr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `tcb_bind_vspace` (kernel gate, selector 145)

**Status: wired.**

**What it does.** Places a fresh task in the address space of a donor task.

**Why it exists.** Threads of one program share an address space.

**Capability presented in `rdi` (rights required).** task WRITE (the TARGET, a fresh task the caller retyped)

| register | parameter | type |
|---|---|---|
| `rsi` | `vspace` | integer |

**Result.** status; the target Task capability (WRITE) is at rdi and vspace is a cptr, at the same depth, of the DONOR Task capability (WRITE) whose address space the target will run in. INVALID for a donor that is not a retyped task, runs in the kernel space, or whose root names no page-table tree, and for a target that is not fresh and unplaced; DENIED for a donor or target capability without WRITE or of another kind, for a donor whose space is bound to another partition, and for a bind that would split the domains; the target is placed in the donor's space; a refused call changes nothing

**Statuses named for this call:** `DENIED`, `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::tcb_bind_vspace(&mut t, cap, vspace);
// r = [rax, rdx]: rax is the status word, rdx the value
```

## Address spaces, frames and caches

Mapping frames with write-xor-execute by capability, ASID pools, cache maintenance.

### `map_frame` (kernel gate, selector 87)

**Status: wired.**

**What it does.** Maps a retyped frame into the task named by task_cptr. Flags: writable or executable, never both. A writable mapping needs an unfrozen frame; an executable one needs a frozen frame and the map-exec authority named by exec_auth_cptr.

**Why it exists.** Write-xor-execute enforced by capability: code can only run after its writes have been frozen.

**Capability presented in `rdi` (rights required).** frame MAP (+ WRITE for a writable mapping)

| register | parameter | type |
|---|---|---|
| `rsi` | `task_cptr` | integer |
| `rdx` | `virt` | integer |
| `r10` | `flags` | integer |
| `r8` | `exec_auth_cptr` | integer |

**Result.** status; ABI 2.0: the frame is the capability at rdi, the Task capability (WRITE) at task_cptr names the address space (same depth r9); flags bit0 = writable, bit1 = executable, nothing above (INVALID); write + execute is WRITE_XOR_EXECUTE (7) whatever else is wrong; an executable mapping (flags == 2) presents the map-exec authority (a Task capability on the exec-map object with MAP + EXECUTE) at exec_auth_cptr (0 = nothing presented = DENIED) and needs a FROZEN frame (NOT_FROZEN otherwise); virt 4 KiB aligned and canonical; the page is installed PRESENT | USER, plus WRITABLE for a writable mapping; a writable mapping needs WRITE on the frame capability and a frame that has NOT been frozen; the mapping holds a reference on the frame; ALREADY_MAPPED when virt already holds a page; a frame capability the caller does not hold answers LOOKUP

**Statuses named for this call:** `DENIED`, `INVALID`, `ALREADY_MAPPED`, `LOOKUP`, `FROZEN`, `NOT_FROZEN`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::map_frame(&mut t, cap, task_cptr, virt, flags, exec_auth_cptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

**Notes.** This row is defined with its conventions fixed; the kernel gate does not serve it yet, so a call answers `NOT_WIRED` today.

### `freeze_frame` (kernel gate, selector 88)

**Status: defined, not served.** The selector and conventions are fixed, but the kernel gate does not serve this selector yet: it answers `NOT_WIRED` and changes nothing.

**What it does.** Drops the frame's write right for good. Its contents stay.

**Why it exists.** Seals code before it is made executable.

**Capability presented in `rdi` (rights required).** frame WRITE

Parameters: none.

**Result.** status; drops the frame's write right for good (until the object is deleted): its contents stay, every later writable mapping is FROZEN; WRITE_XOR_EXECUTE while the frame still has a writable mapping (unmap it first)

**Statuses named for this call:** `FROZEN`, `NOT_WIRED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::freeze_frame(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

**Notes.** This row is defined; the kernel gate does not serve it yet (see map_frame), so a call answers `NOT_WIRED` today.

### `asid_assign` (kernel gate, selector 89)

**Status: wired.**

**What it does.** Binds the address space of a task to an ASID pool.

**Why it exists.** Address-space identifiers avoid full TLB flushes on every switch.

**Capability presented in `rdi` (rights required).** asid-pool WRITE (rdi); the task whose address space is bound is a Task capability with WRITE named by task_cptr (resolved at the same depth, r9)

| register | parameter | type |
|---|---|---|
| `rsi` | `task_cptr` | integer |

**Result.** status; binds the address space of that task to the pool (one pool per space); INVALID for a task that is not placed in an address space, for a space already assigned, and for a capability that is not a pool; EXHAUSTED when the pool table of assignments is full (1,024); no PCID is consumed until its first switch

**Statuses named for this call:** `INVALID`, `EXHAUSTED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::asid_assign(&mut t, cap, task_cptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `frame_flush` (kernel gate, selector 90)

**Status: wired.**

**What it does.** Plans the cache lines to flush for a byte range of a frame and returns the line count; never returns a physical address.

**Why it exists.** Cache maintenance without handing out physical addresses.

**Capability presented in `rdi` (rights required).** frame READ

| register | parameter | type |
|---|---|---|
| `rsi` | `off` | integer |
| `rdx` | `len` | integer |

**Result.** status; Ok(line count): the lines to flush cover bytes [off, off + len) of the frame, never outside it (OUT_OF_FRAME); EMPTY for len 0. The kernel never returns a physical address (an address is the caller's own mapping's business: a flush+reload primitive must not learn where a frame lives)

**Statuses named for this call:** `OUT_OF_FRAME`, `EMPTY`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::frame_flush(&mut t, cap, off, len);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `frame_unify` (kernel gate, selector 91)

**Status: wired.**

**What it does.** The same plan as frame_flush, for making instruction and data views coherent.

**Why it exists.** Needed after writing code and before executing it.

**Capability presented in `rdi` (rights required).** frame READ

| register | parameter | type |
|---|---|---|
| `rsi` | `off` | integer |
| `rdx` | `len` | integer |

**Result.** status; Ok(line count): the same plan and the same range check as frame_flush (x86's instruction cache is coherent with data writes: the arch work is the line walk and a serialising instruction)

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::frame_unify(&mut t, cap, off, len);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `frame_unmap` (kernel gate, selector 92)

**Status: wired.**

**What it does.** Removes the mapping of a frame at a virtual address in a task, without destroying the frame.

**Why it exists.** Unmap by capability.

**Capability presented in `rdi` (rights required).** frame MAP (rdi); the task is a Task capability with WRITE named by task_cptr (resolved at the same depth, r9)

| register | parameter | type |
|---|---|---|
| `rsi` | `task_cptr` | integer |
| `rdx` | `virt` | integer |

**Result.** status; removes the mapping of this frame at virt in that task's address space WITHOUT destroying the frame (4 KiB, 2 MiB or 1 GiB); INVALID for an address this frame is not mapped at. The TLB invalidation is queued by the kernel (`take_tlb_invalidations`): no PCID is returned

**Statuses named for this call:** `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::frame_unmap(&mut t, cap, task_cptr, virt);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `asid_pool_make` (kernel gate, selector 106)

**Status: wired.**

**What it does.** Makes an ASID pool owning a range of PCIDs, from an untyped, using the ASID control.

**Why it exists.** Pools are disjoint, so tasks cannot interfere through TLB tags.

**Capability presented in `rdi` (rights required).** asid-control WRITE (rdi); the untyped is a Memory capability with WRITE named by ut_cptr and the destination CNode a Memory capability with WRITE named by dest_cptr (all resolved at the same depth, r9)

| register | parameter | type |
|---|---|---|
| `rsi` | `ut_cptr` | integer |
| `rdx` | `pcids` | integer |
| `r10` | `dest_cptr` | integer |
| `r8` | `dest_index` | integer |

**Result.** status; Ok(pcids). The new pool capability is installed in the destination CNode's slot dest_index (it must be empty). The pool owns pcids hardware PCIDs (1..= 4095) that no other live pool shares; INVALID for pcids 0 or above 4095, for a presented capability that is not the ASID control, and for a second ASID control; EXHAUSTED when no free range of that size is left or the pool table (64) is full. The ASID control itself is made by retype with RETYPE_KIND_ASID_CONTROL_FLAG (one per kernel)

**Statuses named for this call:** `INVALID`, `EXHAUSTED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::asid_pool_make(&mut t, cap, ut_cptr, pcids, dest_cptr, dest_index);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `task_set_vspace` (kernel gate, selector 142)

**Status: wired.**

**What it does.** Places a confined task in a fresh address space of its own, whose root table is a frame the caller retyped.

**Why it exists.** Every task can have its own address space, built from memory it was given, instead of sharing the kernel's.

**Capability presented in `rdi` (rights required).** schedule authority WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `task` | integer |
| `rdx` | `root_table` | integer |

**Result.** status; Ok(root). rdi is the schedule authority (Task capability on the schedule object, WRITE), task a Task capability (WRITE) for a task retyped and admitted CONFINED (140 with confined = 1), root_table a frame (Memory capability, WRITE, a free 4 KiB frame retyped from an untyped; both cptrs at the caller's depth): the kernel places the task in a fresh address space of its own whose ROOT TABLE IS THAT FRAME (charged to its untyped; never the kernel's or the caller's tree) and which takes every further table page from frames donated with row 148; value is the root (a label, not a physical address). DENIED with the root in value when the kernel is armed with no partition schedule (install one naming that root, row 32, and call again); INVALID for a task that is fresh, not admitted confined, already placed or gone; a refused call changes nothing

**Statuses named for this call:** `DENIED`, `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::task_set_vspace(&mut t, cap, task, root_table);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `vspace_map_frame` (kernel gate, selector 143)

**Status: wired.**

**What it does.** Maps a frame read-only or writable at a 4 KiB aligned address in the address space of a task that has its own space.

**Why it exists.** Give a task memory in its own address space, by capability and without ever making a mapping both writable and executable.

**Capability presented in `rdi` (rights required).** frame MAP (+ WRITE for a writable mapping)

| register | parameter | type |
|---|---|---|
| `rsi` | `task` | integer |
| `rdx` | `virt` | integer |
| `r10` | `flags` | integer |

**Result.** status; rdi is a frame (Memory capability, MAP; WRITE for a writable mapping) retyped from an untyped, task a Task capability (WRITE) for a task placed in a space of its own (INVALID for the kernel's tree or a parked task), virt a 4 KiB aligned canonical address, flags 0 or 1 (writable); any other flags value, an executable mapping included, is INVALID. ALREADY_MAPPED when virt holds a page, FROZEN for a writable mapping of a frozen frame, DENIED without the right; every rule of the by-pointer map applies

**Statuses named for this call:** `DENIED`, `INVALID`, `ALREADY_MAPPED`, `FROZEN`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::vspace_map_frame(&mut t, cap, task, virt, flags);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `frame_freeze` (kernel gate, selector 146)

**Status: wired.**

**What it does.** Drops a frame's write right for good: its contents stay, later writable mappings are refused, and the frame becomes eligible for an executable mapping.

**Why it exists.** Code can only run after its writes have been frozen.

**Capability presented in `rdi` (rights required).** frame WRITE

Parameters: none.

**Result.** status; drops the frame's write right for good (until the object is deleted): its contents stay, every later writable mapping of it is FROZEN, and it becomes eligible for an executable mapping (row 147). rdi is a frame (Memory capability, WRITE); WRITE_XOR_EXECUTE (7) while the frame still has a live writable mapping (unmap it first, row 92); DENIED for the wrong kind or right, an untyped region or a dead object; a refused call changes nothing. The by-pointer form of the Defined row 88

**Statuses named for this call:** `DENIED`, `FROZEN`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::frame_freeze(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `vspace_map_exec` (kernel gate, selector 147)

**Status: wired.**

**What it does.** Maps a frozen frame read and execute, never writable, in the address space of a task that has its own space; needs the map-exec authority.

**Why it exists.** The one way code becomes executable: a frozen frame, plus a separate authority.

**Capability presented in `rdi` (rights required).** frame MAP (rdi); the map-exec authority is a Task capability on the map-exec object with MAP and EXECUTE

| register | parameter | type |
|---|---|---|
| `rsi` | `task` | integer |
| `rdx` | `virt` | integer |
| `r10` | `exec_auth` | integer |

**Result.** status; maps a FROZEN frame read + execute (never writable) at virt in the space of the task named by task (a Task capability, WRITE, placed in a space of its own: INVALID for the kernel's tree or a parked task); exec_auth is the map-exec authority's slot, all cptrs at the caller's depth. DENIED without the authority or with a wrong kind; NOT_FROZEN (9) for a frame that still has its write right; WRITE_XOR_EXECUTE (7) while a writable mapping of the frame is live; ALREADY_MAPPED when virt holds a page

**Statuses named for this call:** `DENIED`, `INVALID`, `ALREADY_MAPPED`, `FROZEN`, `NOT_FROZEN`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::vspace_map_exec(&mut t, cap, task, virt, exec_auth);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `vspace_donate_table` (kernel gate, selector 148)

**Status: wired.**

**What it does.** Turns a free frame the caller owns into a page-table page of a task's own address space.

**Why it exists.** A task's page tables are built from memory it was given, so the kernel never allocates them on its own.

**Capability presented in `rdi` (rights required).** frame WRITE (rdi); the task whose space receives the page is a Task capability with WRITE named by task, same depth

| register | parameter | type |
|---|---|---|
| `rsi` | `task` | integer |

**Result.** status; the frame (Memory capability, WRITE) retyped from an untyped becomes a PAGE-TABLE page of the space of the task named by task (a task placed in a space of its own: INVALID for the kernel's tree, a parked task, or a frame that is not a free 4 KiB frame - mapped, frozen, already a table or writable by a device). It is a table page for good (a data mapping of it is DENIED); a map the donated pages cannot cover is EXHAUSTED and installs nothing; revoking the untyped it came from destroys the space and parks its tasks

**Statuses named for this call:** `DENIED`, `INVALID`, `EXHAUSTED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::vspace_donate_table(&mut t, cap, task);
// r = [rax, rdx]: rax is the status word, rdx the value
```

## Interrupts

IRQ control and per-line handlers that deliver a hardware interrupt as a notification.

### `bind_irq` (kernel gate, selector 42)

**Status: superseded.** Replaced by selector 115. The row is kept forever (the table is append-only) but the kernel never serves it: it answers `NOT_WIRED`.

**What it does.** Superseded by irq_handler_set_notification. Never served; answers NOT_WIRED.

**Why it exists.** Kept because the table is append-only.

**Capability presented in `rdi` (rights required).** device WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `signal_cap_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |

**Result.** SUPERSEDED by 115 irq_handler_set_notification; never served, answers NOT_WIRED. Was: status

**Statuses named for this call:** `NOT_WIRED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::bind_irq(&mut t, cap, signal_cap_ptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `unbind_irq` (kernel gate, selector 43)

**Status: superseded.** Replaced by selector 117. The row is kept forever (the table is append-only) but the kernel never serves it: it answers `NOT_WIRED`.

**What it does.** Superseded by irq_handler_clear. Never served; answers NOT_WIRED.

**Why it exists.** Kept because the table is append-only.

**Capability presented in `rdi` (rights required).** device WRITE

Parameters: none.

**Result.** SUPERSEDED by 117 irq_handler_clear; never served, answers NOT_WIRED. Was: status

**Statuses named for this call:** `NOT_WIRED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::unbind_irq(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `irq_handler_get` (kernel gate, selector 114)

**Status: wired.**

**What it does.** Installs the handler capability for an interrupt line into an empty slot. At most one live handler per line.

**Why it exists.** Only whoever holds the IRQ control can hand a driver a line.

**Capability presented in `rdi` (rights required).** device(IRQ_CONTROL_OBJECT) WRITE at rdi

| register | parameter | type |
|---|---|---|
| `rsi` | `line` | integer |
| `rdx` | `dest_cptr` | integer |

**Result.** status; installs the handler capability for `line` (Device, READ|WRITE) in the EMPTY slot dest_cptr (same depth) as a child of the control's slot; at most one live handler per line. DENIED for a capability that is not the control or lacks WRITE; LOOKUP for an unresolvable control; ALREADY_MAPPED for an occupied destination; INVALID for a line out of range or already issued. A refusal issues nothing

**Statuses named for this call:** `DENIED`, `INVALID`, `ALREADY_MAPPED`, `LOOKUP`, `EMPTY`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::irq_handler_get(&mut t, cap, line, dest_cptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `irq_handler_set_notification` (kernel gate, selector 115)

**Status: wired.**

**What it does.** Binds the handler's line to a notification capability: its badge is what the line signals.

**Why it exists.** An interrupt becomes a notification the driver can wait on.

**Capability presented in `rdi` (rights required).** device(IRQ handler) READ|WRITE at rdi

| register | parameter | type |
|---|---|---|
| `rsi` | `signal_cptr` | integer |

**Result.** status; binds the handler's line to the notification capability (WRITE) at signal_cptr (same depth): its badge is what the line signals. LOOKUP for an empty or unresolvable handler slot or signal slot; DENIED for a handler slot of the wrong kind or without READ|WRITE, a capability that is not a live handler, or a signal capability that is not a notification with WRITE; INVALID when the line is already bound or the handler is stale (revoked)

**Statuses named for this call:** `DENIED`, `INVALID`, `LOOKUP`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
// Deliver interrupt line `line` to a driver as a notification (handler slot `h`, signal capability slot `sig`)
kernel::irq_handler_get(&mut t, irq_control, line, h);
kernel::irq_handler_set_notification(&mut t, h, sig);
loop {
    let w = kernel::wait(&mut t, wait_cap)[1];   // the interrupt arrived
    /* clear the device's own interrupt cause first */
    kernel::irq_handler_ack(&mut t, h);            // then unmask the line
}
```

### `irq_handler_ack` (kernel gate, selector 116)

**Status: wired.**

**What it does.** Unmasks the line after a delivered interrupt.

**Why it exists.** The line stays masked until the driver says it has dealt with the device.

**Capability presented in `rdi` (rights required).** device(IRQ handler) READ|WRITE at rdi

Parameters: none.

**Result.** status; unmasks the line: after a delivered interrupt the line stays masked until this ack. LOOKUP for an empty or unresolvable slot; DENIED for the wrong kind, a missing right or a capability that is not a live handler; INVALID for a stale handler

**Statuses named for this call:** `DENIED`, `INVALID`, `LOOKUP`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::irq_handler_ack(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `irq_handler_clear` (kernel gate, selector 117)

**Status: wired.**

**What it does.** Removes the line's binding and unmasks it. The handler stays valid.

**Why it exists.** Detach a notification without destroying the right.

**Capability presented in `rdi` (rights required).** device(IRQ handler) READ|WRITE at rdi

Parameters: none.

**Result.** status; removes the line's notification binding and unmasks it; the handler stays valid. LOOKUP for an empty or unresolvable slot; DENIED for the wrong kind, a missing right or a capability that is not a live handler; INVALID when nothing is bound or the handler is stale

**Statuses named for this call:** `DENIED`, `INVALID`, `LOOKUP`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::irq_handler_clear(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `irq_control_revoke` (kernel gate, selector 118)

**Status: wired.**

**What it does.** Removes the line's binding, mask and handler; every capability of the old generation is dead.

**Why it exists.** Take a line away from a driver for good, and be able to issue it again.

**Capability presented in `rdi` (rights required).** device(IRQ_CONTROL_OBJECT) WRITE at rdi

| register | parameter | type |
|---|---|---|
| `rsi` | `line` | integer |

**Result.** status; removes the line's binding, mask and handler entry (the line can be issued again); every capability of the old generation is dead (INVALID from then on). LOOKUP for an empty or unresolvable slot; DENIED for a capability that is not the control or lacks WRITE; INVALID for a line out of range or with no live handler

**Statuses named for this call:** `DENIED`, `INVALID`, `LOOKUP`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::irq_control_revoke(&mut t, cap, line);
// r = [rax, rdx]: rax is the status word, rdx the value
```

## I/O ports

x86 port ranges as capability objects.

### `ioport_issue` (kernel gate, selector 110)

**Status: wired.**

**What it does.** Issues an I/O-port capability over a range (read, write or both) into an empty slot. Refused for ports no capability may cover.

**Why it exists.** Port access by capability instead of by privilege level.

**Capability presented in `rdi` (rights required).** device(IOPORT_CONTROL_OBJECT) WRITE (the IOPortControl, named by rdi)

| register | parameter | type |
|---|---|---|
| `rsi` | `first` | integer |
| `rdx` | `last` | integer |
| `r10` | `rights` | integer |
| `r8` | `dest_cptr` | integer |

**Result.** status; the new IOPort capability (device, READ and/or WRITE) is installed in the EMPTY slot dest_cptr as a child of the control's slot; first and last at most 0xFFFF and first <= last, rights only READ (in) and/or WRITE (out), else INVALID; DENIED for a range containing a port no capability may cover (PCI configuration 0xCF8..0xCFF, the 8259s, the PIT, PS/2, 0x61, CMOS, COM1/COM2); BUSY when the range overlaps a live one; EXHAUSTED when the table is full

**Statuses named for this call:** `DENIED`, `INVALID`, `EXHAUSTED`, `BUSY`, `EMPTY`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::ioport_issue(&mut t, cap, first, last, rights, dest_cptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `ioport_in` (kernel gate, selector 111)

**Status: wired.**

**What it does.** Checks that a read of 1, 2 or 4 bytes lies inside the capability's range; if so the architecture layer performs it and the value is returned in rdx. The kernel decides, it does not touch the port.

**Why it exists.** A driver reads a device register it was explicitly granted.

**Capability presented in `rdi` (rights required).** ioport READ (named by rdi)

| register | parameter | type |
|---|---|---|
| `rsi` | `port` | integer |
| `rdx` | `width` | integer |

**Result.** status; OK means the access is allowed and the ARCH LAYER performs it (the kernel decides, it does not touch the port): and the word read goes to rdx; width 1, 2 or 4 (else INVALID) and the whole access inside the range the capability was issued for (else DENIED); INVALID for a revoked capability; a refusal performs nothing; not ring-eligible

**Statuses named for this call:** `OK`, `DENIED`, `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::ioport_in(&mut t, cap, port, width);
// r = [rax, rdx]: rax is the status word, rdx the value
```

**Notes.** The kernel only decides. An allowed access is performed by the architecture layer and the value read comes back in `rdx`. A refusal performs nothing. The call is not eligible for a submission ring.

### `ioport_out` (kernel gate, selector 112)

**Status: wired.**

**What it does.** As ioport_in, for writes.

**Why it exists.** A driver writes a granted device register.

**Capability presented in `rdi` (rights required).** ioport WRITE (named by rdi)

| register | parameter | type |
|---|---|---|
| `rsi` | `port` | integer |
| `rdx` | `width` | integer |
| `r10` | `value` | integer |

**Result.** status; OK means the access is allowed and the ARCH LAYER performs it; width 1, 2 or 4 and the value fits the width (else INVALID); the whole access inside the issued range (else DENIED); INVALID for a revoked capability; a refusal performs nothing; not ring-eligible

**Statuses named for this call:** `OK`, `DENIED`, `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::ioport_out(&mut t, cap, port, width, value);
// r = [rax, rdx]: rax is the status word, rdx the value
```

**Notes.** An allowed access is performed by the architecture layer. A refusal performs nothing. The call is not eligible for a submission ring.

### `ioport_revoke` (kernel gate, selector 113)

**Status: wired.**

**What it does.** Revokes the live range containing a port; its capabilities die and the ports can be issued again.

**Why it exists.** Take a device away from a driver.

**Capability presented in `rdi` (rights required).** device(IOPORT_CONTROL_OBJECT) WRITE (the IOPortControl, named by rdi)

| register | parameter | type |
|---|---|---|
| `rsi` | `port` | integer |

**Result.** status; revokes the live range that contains port (INVALID when none): every capability issued for it is dead at once and its ports can be issued again

**Statuses named for this call:** `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::ioport_revoke(&mut t, cap, port);
// r = [rax, rdx]: rax is the status word, rdx the value
```

## DMA address spaces (IO spaces)

IOMMU address spaces: what a device may reach by DMA, controlled by capability.

### `iospace_create` (kernel gate, selector 93)

**Status: wired.**

**What it does.** Creates an IO-space capability (a DMA address space) in an empty slot, using the IOMMU authority.

**Why it exists.** A device can only reach memory its space maps.

**Capability presented in `rdi` (rights required).** device(IOMMU_OBJECT) WRITE (the IOMMU authority, named by rdi)

| register | parameter | type |
|---|---|---|
| `rsi` | `dest_cptr` | integer |

**Result.** status; the new IO-space capability (device, READ|WRITE|MAP) is installed in the EMPTY slot dest_cptr as a child of the authority's slot, never written out as bytes; an occupied destination is ALREADY_MAPPED, an unresolvable one LOOKUP/INVALID, INVALID at the space limit (16), NO_IOMMU when no backend is attached; nothing is asked of the backend when the destination is refused

**Statuses named for this call:** `INVALID`, `ALREADY_MAPPED`, `NO_IOMMU`, `LOOKUP`, `EMPTY`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::iospace_create(&mut t, cap, dest_cptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `iospace_attach_device` (kernel gate, selector 94)

**Status: wired.**

**What it does.** Binds a PCI device to the space. From then its DMA reaches only what the space maps.

**Why it exists.** Confines a device. A device in no space is denied, never pass-through.

**Capability presented in `rdi` (rights required).** iospace WRITE (the space, named by rdi)

| register | parameter | type |
|---|---|---|
| `rsi` | `devid` | integer |
| `rdx` | `auth_cptr` | integer |

**Result.** status; devid = bus << 8 | devfn (at most 0xFFFF, else INVALID); the IOMMU authority is a capability in the caller's CSpace named by auth_cptr (which devices a driver may be given is the bootstrap's decision, not the driver's); BUSY when the device is already in a space; EXHAUSTED when 256 devices are bound; from now on its DMA reaches only what the space maps

**Statuses named for this call:** `INVALID`, `EXHAUSTED`, `BUSY`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::iospace_attach_device(&mut t, cap, devid, auth_cptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `iospace_map` (kernel gate, selector 95)

**Status: wired.**

**What it does.** Maps a frame at a device address (iova), read-only or writable. The kernel resolves the real physical address.

**Why it exists.** Gives a device a window onto exactly the memory the driver chose.

**Capability presented in `rdi` (rights required).** iospace MAP (the space, named by rdi)

| register | parameter | type |
|---|---|---|
| `rsi` | `iova` | integer |
| `rdx` | `frame_cptr` | integer |
| `r10` | `write` | integer |

**Result.** status; write is 0 or 1 (anything else INVALID); iova 4 KiB aligned and below 2^48; the frame is a Memory capability in the caller's CSpace named by frame_cptr: it needs MAP (+ WRITE for a writable mapping) and names a retyped frame, whose REAL physical address the kernel resolves (a caller never supplies one); WRITE_EXECUTE for a writable mapping of a frozen frame; ALREADY_MAPPED when the iova is taken; EXHAUSTED when 4,096 mappings are held

**Statuses named for this call:** `INVALID`, `EXHAUSTED`, `ALREADY_MAPPED`, `WRITE_EXECUTE`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::iospace_map(&mut t, cap, iova, frame_cptr, write);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `iospace_unmap` (kernel gate, selector 96)

**Status: wired.**

**What it does.** Removes one mapping and returns only after the IOMMU has dropped every cached translation.

**Why it exists.** A page that was unmapped cannot still be written by the device.

**Capability presented in `rdi` (rights required).** iospace MAP (the space, named by rdi)

| register | parameter | type |
|---|---|---|
| `rsi` | `iova` | integer |

**Result.** status; returns only after the unit has dropped every cached translation of the page (HARDWARE and the kernel's record is KEPT when it did not answer); UNMAPPED when nothing is mapped there

**Statuses named for this call:** `UNMAPPED`, `HARDWARE`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::iospace_unmap(&mut t, cap, iova);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `iospace_invalidate` (kernel gate, selector 97)

**Status: wired.**

**What it does.** Drops every cached translation of the space and waits.

**Why it exists.** Resynchronize after bulk changes.

**Capability presented in `rdi` (rights required).** iospace MAP (the space, named by rdi)

Parameters: none.

**Result.** status; drops every cached translation of the space and waits

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::iospace_invalidate(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `iospace_destroy` (kernel gate, selector 98)

**Status: wired.**

**What it does.** Unmaps every page, returns every device to deny, kills every capability to the space.

**Why it exists.** Clean teardown of a driver's DMA world.

**Capability presented in `rdi` (rights required).** iospace WRITE (the space, named by rdi)

| register | parameter | type |
|---|---|---|
| `rsi` | `auth_cptr` | integer |

**Result.** status; the IOMMU authority is a capability in the caller's CSpace named by auth_cptr; every page is unmapped and every device returns to DENY (never pass-through); every capability for the space is dead afterwards (the revoke)

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::iospace_destroy(&mut t, cap, auth_cptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

## Device report slots

A small diagnostic latch on a device capability: a driver's owner reads back what the driver stored, and it carries no authority.

### `driver_report` (kernel gate, selector 150)

**Status: wired.**

**What it does.** Stores one word in a numbered report slot (0 to 15) of a device the caller holds WRITE on.

**Why it exists.** A driver leaves a diagnostic value that its owner can read back later; the slot carries no authority.

**Capability presented in `rdi` (rights required).** device WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `slot` | integer |
| `rdx` | `value` | integer |

**Result.** status; rdi is a Device capability (WRITE) the caller holds: the kernel stores value in report slot `slot` (0.. DRIVER_REPORT_SLOTS = 16), a diagnostic latch a driver's owner reads back, carrying no authority. INVALID for a slot out of range; DENIED for the wrong kind or without WRITE; a refused call stores nothing

**Statuses named for this call:** `DENIED`, `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::driver_report(&mut t, cap, slot, value);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `driver_report_read` (kernel gate, selector 151)

**Status: wired.**

**What it does.** Reads back the last word stored in a report slot of a device the caller holds READ on, or the maximum value when nothing was stored.

**Why it exists.** The owner of a driver reads what it reported, without any power over the device.

**Capability presented in `rdi` (rights required).** device READ

| register | parameter | type |
|---|---|---|
| `rsi` | `slot` | integer |

**Result.** status; rdi is a Device capability (READ) the caller holds: Ok(word) is the last value stored in report slot `slot` by driver_report, or u64::MAX when nothing was stored. INVALID for a slot out of range; DENIED for the wrong kind or without READ

**Statuses named for this call:** `DENIED`, `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::driver_report_read(&mut t, cap, slot);
// r = [rax, rdx]: rax is the status word, rdx the value
```

## Submission and completion rings

Batch many calls into one kernel entry.

### `ring_create` (kernel gate, selector 120)

**Status: wired.**

**What it does.** Creates a ring object in caller-supplied memory and installs it as a memory capability.

**Why it exists.** Batching: many calls, one kernel entry.

**Capability presented in `rdi` (rights required).** untyped WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `geometry` | integer |
| `rdx` | `base` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |
| `r10` | `dest_cptr` | integer |
| `r8` | `dest_index` | integer |

**Result.** status; geometry = sq_bits | cq_bits << 8 (1 <= sq_bits <= cq_bits <= MAX_BITS); the caller supplies the ring memory at base (4096-aligned, HDR_BYTES + SQE_BYTES << sq_bits + CQE_BYTES << cq_bits bytes of memory it can read and write, probed by writing the header); the ring OBJECT (charged one frame to the untyped, bound to the caller) is installed as a Memory capability (READ | WRITE) in the CNode capability at dest_cptr, slot dest_index

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::ring_create(&mut t, cap, geometry, base, dest_cptr, dest_index);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `ring_enter` (kernel gate, selector 121)

**Status: wired.**

**What it does.** Serves up to 32 queued submissions in order, one completion each.

**Why it exists.** Amortizes the cost of entering the kernel.

**Capability presented in `rdi` (rights required).** ring (memory) WRITE, the ring's owner only

| register | parameter | type |
|---|---|---|
| `rsi` | `to_submit` | length or count |

**Result.** status; Ok with rdx = consumed | pending << 32 (serves at most min(to_submit, pending, ENTER_BUDGET) submissions in order, one completion each), or RING_FULL when nothing could be served because the completion queue is full; INVALID when the ring memory is unreadable/unwritable or an index is corrupt

**Statuses named for this call:** `INVALID`, `RING_FULL`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
// Serve queued submissions of a ring you created with one kernel entry
let r = kernel::ring_enter(&mut t, ring_cap, /*to_submit*/ 8);
let consumed = r[1] & 0xFFFF_FFFF;
let pending  = r[1] >> 32;
```

## Virtual machines (VCPU and EPT/NPT)

The kernel part of virtualization: VCPU objects, guest-physical address spaces, injection.

### `hv_protect` (kernel gate, selector 69)

**Status: wired.**

**What it does.** Declares a host-physical range no guest may be mapped onto.

**Why it exists.** Keeps the host's own memory out of reach of every guest.

**Capability presented in `rdi` (rights required).** device(HV_OBJECT) WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `base` | integer |
| `rdx` | `len` | length or count |

**Result.** status; declares a host-physical range no guest may be mapped onto (HitsProtected afterwards)

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::hv_protect(&mut t, cap, base, len);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `ept_create` (kernel gate, selector 70)

**Status: superseded.** Replaced by selector 136. The row is kept forever (the table is append-only) but the kernel never serves it: it answers `NOT_WIRED`.

**What it does.** Defined, not served: superseded by retype with the EPT kind and ept_map_frame.

**Why it exists.** Kept because the table is append-only.

**Capability presented in `rdi` (rights required).** device(HV_OBJECT) WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `out_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |

**Result.** SUPERSEDED by 136 ept_retype; never served, answers NOT_WIRED. Was: status; the new EPT capability is written at out_ptr (same convention as retype)

**Statuses named for this call:** `NOT_WIRED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::ept_create(&mut t, cap, out_ptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `ept_map` (kernel gate, selector 71)

**Status: superseded.** Replaced by selector 132. The row is kept forever (the table is append-only) but the kernel never serves it: it answers `NOT_WIRED`.

**What it does.** Defined, not served: superseded by ept_map_frame.

**Why it exists.** Kept because the table is append-only.

**Capability presented in `rdi` (rights required).** ept MAP

| register | parameter | type |
|---|---|---|
| `rsi` | `gpa` | integer |
| `rdx` | `frame_cap_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |
| `r10` | `perms` | integer |

**Result.** SUPERSEDED by 132 ept_map_frame; never served, answers NOT_WIRED. Was: status; perms R=1 W=2 X=4, never W+X; gpa 4 KiB aligned; the frame capability needs MAP plus READ / WRITE for what is mapped

**Statuses named for this call:** `NOT_WIRED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::ept_map(&mut t, cap, gpa, frame_cap_ptr, perms);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `ept_unmap` (kernel gate, selector 72)

**Status: wired.**

**What it does.** Removes a guest-physical mapping.

**Why it exists.** Take memory away from a guest.

**Capability presented in `rdi` (rights required).** ept MAP

| register | parameter | type |
|---|---|---|
| `rsi` | `gpa` | integer |

**Result.** status

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::ept_unmap(&mut t, cap, gpa);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `ept_access` (kernel gate, selector 73)

**Status: wired.**

**What it does.** Asks what the hardware walk would answer for a guest access (read, write or execute): the host frame offset, UNMAPPED or PROTECTION.

**Why it exists.** Lets a VMM resolve an EPT violation without guessing.

**Capability presented in `rdi` (rights required).** ept READ

| register | parameter | type |
|---|---|---|
| `rsi` | `gpa` | integer |
| `rdx` | `access` | integer |

**Result.** status; `access` is ONE of the EPT bits (R=1, W=2, X=4, exactly one set) = Read / Write / Execute; Ok(host frame offset) in rdx, or UNMAPPED / PROTECTION, exactly as the hardware walk must answer

**Statuses named for this call:** `UNMAPPED`, `PROTECTION`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::ept_access(&mut t, cap, gpa, access);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `vcpu_create` (kernel gate, selector 74)

**Status: superseded.** Replaced by selector 48. The row is kept forever (the table is append-only) but the kernel never serves it: it answers `NOT_WIRED`.

**What it does.** Defined, not served: superseded by retype with the VCPU kind and vcpu_bind.

**Why it exists.** Kept because the table is append-only.

**Capability presented in `rdi` (rights required).** device(HV_OBJECT) WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `ept_cap_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |
| `rdx` | `task_cap_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |
| `r10` | `out_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |

**Result.** SUPERSEDED by 48 retype with RETYPE_KIND_VCPU_FLAG, then 128 vcpu_bind; never served, answers NOT_WIRED. Was: status; presented by pointer: the HV capability (WRITE) in rdi, the EPT capability (READ) at ept_cap_ptr, the task capability (WRITE) at task_cap_ptr; the minted VCPU capability is written at out_ptr, as retype does; one VCPU per task

**Statuses named for this call:** `NOT_WIRED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::vcpu_create(&mut t, cap, ept_cap_ptr, task_cap_ptr, out_ptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `vcpu_set_regs` (kernel gate, selector 75)

**Status: wired.**

**What it does.** Writes the 18 guest registers while the VCPU is stopped.

**Why it exists.** Set up a guest's entry state.

**Capability presented in `rdi` (rights required).** vcpu WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `regs_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |

**Result.** status; 18 u64 words: rip, rsp, rflags, rax rcx rdx rbx rbp rsi rdi r8..r15; INVALID while the VCPU state is Running or Dead

**Statuses named for this call:** `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::vcpu_set_regs(&mut t, cap, regs_ptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `vcpu_regs` (kernel gate, selector 76)

**Status: wired.**

**What it does.** Reads the 18 guest registers.

**Why it exists.** Inspect a guest after an exit.

**Capability presented in `rdi` (rights required).** vcpu READ

| register | parameter | type |
|---|---|---|
| `rsi` | `out_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |

**Result.** status; the same 18 words are written at out_ptr

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::vcpu_regs(&mut t, cap, out_ptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `vcpu_exit_info` (kernel gate, selector 77)

**Status: wired.**

**What it does.** Reads five words describing the last VM exit.

**Why it exists.** Decide how to handle an exit.

**Capability presented in `rdi` (rights required).** vcpu READ

| register | parameter | type |
|---|---|---|
| `rsi` | `out_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |

**Result.** status; 5 u64 words at out_ptr: reason, qualification, gpa, rip, instr_len

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::vcpu_exit_info(&mut t, cap, out_ptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `vcpu_start` (kernel gate, selector 78)

**Status: wired.**

**What it does.** Moves a VCPU from stopped to runnable.

**Why it exists.** Start the guest.

**Capability presented in `rdi` (rights required).** vcpu WRITE

Parameters: none.

**Result.** status; Stopped -> Runnable

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
// Start a guest: registers are set while the VCPU is stopped, then it runs
kernel::vcpu_set_regs(&mut t, vcpu, regs.as_ptr() as u64);
kernel::vcpu_start(&mut t, vcpu);
// exits arrive at the VMM's fault handler as fault messages (kind 7); answer with reply (0 = resume)
```

### `vcpu_destroy` (kernel gate, selector 79)

**Status: wired.**

**What it does.** Destroys the VCPU.

**Why it exists.** Tear down a guest CPU.

**Capability presented in `rdi` (rights required).** vcpu WRITE

Parameters: none.

**Result.** status

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::vcpu_destroy(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `vcpu_state` (kernel gate, selector 80)

**Status: wired.**

**What it does.** Returns the VCPU state: stopped 0, runnable 1, running 2, waiting 3, dead 4.

**Why it exists.** Poll the guest.

**Capability presented in `rdi` (rights required).** vcpu READ

Parameters: none.

**Result.** status; Ok(state) in rdx, numbered in the order of the VcpuState enum: Stopped 0, Runnable 1, Running 2, Waiting 3, Dead 4

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::vcpu_state(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `vcpu_exit_count` (kernel gate, selector 81)

**Status: wired.**

**What it does.** Returns the number of exits so far.

**Why it exists.** Measurement and monitoring.

**Capability presented in `rdi` (rights required).** vcpu READ

Parameters: none.

**Result.** status; Ok(exits so far) in rdx

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::vcpu_exit_count(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `vcpu_bind` (kernel gate, selector 128)

**Status: wired.**

**What it does.** Binds a VCPU once and for life to a task and an EPT/NPT space of the same hardware family.

**Why it exists.** A guest CPU runs in exactly one guest-physical space.

**Capability presented in `rdi` (rights required).** vcpu WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `task_cptr` | integer |
| `rdx` | `ept_cptr` | integer |

**Result.** status; binds the VCPU, ONCE and for life, to the task named by task_cptr (a Task capability with WRITE, a live task, one VCPU per task) and the EPT / NPT space named by ept_cptr (a Memory capability with MAP, made for the VCPU's own hardware family): INVALID when it is already bound, Dead, the task is gone or already carries a VCPU, or the families differ; DENIED for a capability of the wrong kind or without the right

**Statuses named for this call:** `DENIED`, `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::vcpu_bind(&mut t, cap, task_cptr, ept_cptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `vcpu_inject` (kernel gate, selector 129)

**Status: wired.**

**What it does.** Makes an external interrupt vector pending in the guest.

**Why it exists.** Deliver a virtual device interrupt.

**Capability presented in `rdi` (rights required).** vcpu WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `vector` | integer |

**Result.** status; makes the external interrupt vector pending (vector 0..= 255, anything larger INVALID, never truncated); it is injected at the first entry the guest can take it (IF set, no STI / MOV-SS shadow), else the kernel asks for a window

**Statuses named for this call:** `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::vcpu_inject(&mut t, cap, vector);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `vcpu_inject_event` (kernel gate, selector 130)

**Status: wired.**

**What it does.** Injects one event: external interrupt, NMI, hardware exception or software interrupt, validated at the call.

**Why it exists.** Exceptions and NMIs for guests.

**Capability presented in `rdi` (rights required).** vcpu WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `kind` | integer |
| `rdx` | `vector` | integer |
| `r10` | `error` | integer |
| `r8` | `insn_len` | integer |

**Result.** status; injects one event: kind 0 external, 1 NMI, 2 hardware exception, 3 software interrupt (else INVALID); error = code | 1 << 32 when an error code is present (a code without the presence bit, or bits 33 and up, INVALID); vector and insn_len 0..= 255 exactly. Validated at the call against what Intel and AMD refuse at entry (INVALID, nothing queued); ONE non-external event is pending at a time (a second is INVALID and the first is kept)

**Statuses named for this call:** `INVALID`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::vcpu_inject_event(&mut t, cap, kind, vector, error, insn_len);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `vcpu_pending` (kernel gate, selector 131)

**Status: wired.**

**What it does.** Reads six words describing pending events.

**Why it exists.** Let a VMM see what is still queued.

**Capability presented in `rdi` (rights required).** vcpu READ

| register | parameter | type |
|---|---|---|
| `rsi` | `out_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |

**Result.** status; 6 u64 words at out_ptr: pending external vectors, event present, its kind, vector, error code | 1 << 32 when present, instruction length (the record is probed writable before anything is read)

**Statuses:** `OK`, plus `LOOKUP` and `DENIED` from the capability presented.

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::vcpu_pending(&mut t, cap, out_ptr);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `ept_map_frame` (kernel gate, selector 132)

**Status: wired.**

**What it does.** Maps a 4 KiB guest-physical page to a host frame. A guest never gets more rights than the mapper held.

**Why it exists.** Give a guest memory, with the same write-xor-execute discipline as the host.

**Capability presented in `rdi` (rights required).** ept MAP

| register | parameter | type |
|---|---|---|
| `rsi` | `gpa` | integer |
| `rdx` | `frame_cptr` | integer |
| `r10` | `perms` | integer |

**Result.** status; maps the 4 KiB guest-physical page gpa to the host frame named by frame_cptr (a Memory capability with MAP, plus READ for read or execute and WRITE for write: the guest never gets more than the mapper held); perms R = 1, W = 2, X = 4 and no other bit (INVALID); ALREADY_MAPPED, WRITE_EXECUTE, HITS_PROTECTED as the kernel answers; an NPT space also refuses execute-only and write-only (INVALID)

**Statuses named for this call:** `INVALID`, `ALREADY_MAPPED`, `WRITE_EXECUTE`, `HITS_PROTECTED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::ept_map_frame(&mut t, cap, gpa, frame_cptr, perms);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `ept_destroy` (kernel gate, selector 133)

**Status: wired.**

**What it does.** Deletes a guest space, its mappings and every capability to it, and leaves every virtual CPU bound to it dead.

**Why it exists.** Tear a guest down completely; the memory charged for the space stays charged until its untyped region is revoked.

**Capability presented in `rdi` (rights required).** ept WRITE

Parameters: none.

**Result.** status; deletes the space, its mappings and every capability to it (they stop verifying), and leaves every VCPU bound to it Dead; WRITE on the EPT capability is required (a READ or MAP-only capability is DENIED); the frame the space was charged to its untyped stays charged until that untyped is revoked

**Statuses named for this call:** `DENIED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::ept_destroy(&mut t, cap);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `vcpu_inject_virq` (kernel gate, selector 134)

**Status: wired.**

**What it does.** ARM only: makes a virtual interrupt pending in the guest at a given priority; it is written to a free list register at the next guest entry.

**Why it exists.** Deliver a virtual device interrupt to an ARM guest.

**Capability presented in `rdi` (rights required).** vcpu WRITE

| register | parameter | type |
|---|---|---|
| `rsi` | `intid` | integer |
| `rdx` | `priority` | integer |

**Result.** status; ARM VCPUs only (made by retype with RETYPE_KIND_VCPU_FLAG | 2; any other VCPU is INVALID): makes the virtual interrupt intid pending at priority (a lower value is more urgent); intid 16..= 1019 and priority 0..= 255 exactly (anything else INVALID, never truncated: 2^32 + 42 is not 42); a repeat of a pending intid is one interrupt (OK); EXHAUSTED when 32 distinct INTIDs are already pending; it is written into a free GICH list register at the next entry (highest priority first, what does not fit stays pending)

**Statuses named for this call:** `OK`, `INVALID`, `EXHAUSTED`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::vcpu_inject_virq(&mut t, cap, intid, priority);
// r = [rax, rdx]: rax is the status word, rdx the value
```

### `ept_retype` (kernel gate, selector 136)

**Status: wired.**

**What it does.** Creates an empty guest-physical space (Intel EPT or AMD NPT) out of an untyped region, and puts its capability in a slot of a destination CNode.

**Why it exists.** Guest spaces are ordinary retyped objects, charged to the memory that made them, and revoking that memory deletes the space.

**Capability presented in `rdi` (rights required).** untyped WRITE (rdi); the hypervisor authority (Device, HV_OBJECT, WRITE) at hv_cptr; the destination CNode is a Memory capability with WRITE named by dest_cptr

| register | parameter | type |
|---|---|---|
| `rsi` | `hv_cptr` | integer |
| `rdx` | `family` | integer |
| `r10` | `dest_cptr` | integer |
| `r8` | `dest_index` | integer |

**Result.** status; Ok(1). The untyped is charged ONE frame for an EMPTY guest-physical space whose capability (Memory, READ | WRITE | MAP | GRANT) lands in slot dest_index of the destination CNode as a child of the untyped, so revoking the untyped deletes the space. family is exactly 0 (Intel EPT) or 1 (AMD NPT); 2 is reserved for the ARM stage-2 space; anything else is INVALID, never masked; a VCPU of the other family cannot bind to the space. ABI 2.0: hv_cptr, dest_cptr are cptrs at the depth in r9. DENIED without the hypervisor authority (wrong kind, wrong object, no WRITE) or without WRITE on the untyped; EXHAUSTED when the region or the space table is full; an occupied destination slot is refused and nothing is made

**Statuses named for this call:** `DENIED`, `INVALID`, `EXHAUSTED`, `EMPTY`. (`LOOKUP` and `DENIED` can always arise from the capability presented; see the status-word table.)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = kernel::ept_retype(&mut t, cap, hv_cptr, family, dest_cptr, dest_index);
// r = [rax, rdx]: rax is the status word, rdx the value
```

