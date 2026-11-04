# Driver-domain gate reference

The 14 calls a ring-3 driver domain makes with `int 0x81`. A refused call returns `0xFFFFFFFFFFFFFFFF` (`REFUSED`) and the kernel records the reason. See [Driver domains](domains.md) for the model, grants and refusal names.

Registers: `rax` = selector, `rdi`, `rsi`, `rdx`, `r10` = arguments, result in `rax`. Every other register is preserved.

## Index

| selector | call |
|---|---|
| 1 | [`yield`](#yield-domain-gate-selector-1) |
| 2 | [`exit`](#exit-domain-gate-selector-2) |
| 3 | [`log`](#log-domain-gate-selector-3) |
| 4 | [`pci_read`](#pci_read-domain-gate-selector-4) |
| 5 | [`pci_write`](#pci_write-domain-gate-selector-5) |
| 6 | [`ipc_send`](#ipc_send-domain-gate-selector-6) |
| 7 | [`ipc_recv`](#ipc_recv-domain-gate-selector-7) |
| 8 | [`dma_check`](#dma_check-domain-gate-selector-8) |
| 9 | [`irq_poll`](#irq_poll-domain-gate-selector-9) |
| 10 | [`grant_query`](#grant_query-domain-gate-selector-10) |
| 11 | [`port_in`](#port_in-domain-gate-selector-11) |
| 12 | [`port_out`](#port_out-domain-gate-selector-12) |
| 39 | [`notif_wait`](#notif_wait-domain-gate-selector-39) |
| 40 | [`kernel_gate`](#kernel_gate-domain-gate-selector-40) |

## Calls

### `yield` (domain gate, selector 1)

**Status: wired.**

**What it does.** Gives up the rest of the slice.

**Why it exists.** Cooperation.

Parameters: none.

**Result.** 0; the domain resumes on its next slice

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = domain::yield_now(&mut t);
// r = [rax, rdx]: rax is the result, or REFUSED when the call is refused
```

### `exit` (domain gate, selector 2)

**Status: wired.**

**What it does.** Stops the domain; status 0 means finished, anything else counts as a fault against its restart budget.

**Why it exists.** Orderly stop.

| register | parameter | type |
|---|---|---|
| `rdi` | `status` | integer |

**Result.** does not return

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = domain::exit(&mut t, status);
// r = [rax, rdx]: rax is the result, or REFUSED when the call is refused
```

### `log` (domain gate, selector 3)

**Status: wired.**

**What it does.** Writes up to 200 bytes from the domain's own memory to the kernel log.

**Why it exists.** The only output a domain has by default.

| register | parameter | type |
|---|---|---|
| `rdi` | `ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |
| `rsi` | `len` | length or count |

**Result.** 0, or REFUSED

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
// A domain writes a line to the kernel log
let msg = b"driver ready";
let r = domain::log(&mut t, msg.as_ptr() as u64, msg.len() as u64);
```

### `pci_read` (domain gate, selector 4)

**Status: wired.**

**What it does.** Reads PCI configuration space of a function the domain was granted, width 1, 2 or 4.

**Why it exists.** Drivers must read their device's configuration.

| register | parameter | type |
|---|---|---|
| `rdi` | `bdf` | integer |
| `rsi` | `offset` | integer |
| `rdx` | `width` | integer |

**Result.** the value, or REFUSED

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = domain::pci_read(&mut t, bdf, offset, width);
// r = [rax, rdx]: rax is the result, or REFUSED when the call is refused
```

### `pci_write` (domain gate, selector 5)

**Status: wired.**

**What it does.** Writes only the command register (bits 0, 1, 2, 10) and the interrupt-line byte; never BARs, ROM or capabilities.

**Why it exists.** A driver can enable its device but never remap it.

| register | parameter | type |
|---|---|---|
| `rdi` | `bdf` | integer |
| `rsi` | `offset` | integer |
| `rdx` | `width` | integer |
| `r10` | `value` | integer |

**Result.** 0, or REFUSED

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = domain::pci_write(&mut t, bdf, offset, width, value);
// r = [rax, rdx]: rax is the result, or REFUSED when the call is refused
```

### `ipc_send` (domain gate, selector 6)

**Status: wired.**

**What it does.** Sends a 16-byte message on an endpoint the domain holds a send right for.

**Why it exists.** Domain-to-domain and domain-to-kernel messages.

| register | parameter | type |
|---|---|---|
| `rdi` | `endpoint` | integer |
| `rsi` | `msg_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |

**Result.** 0, or REFUSED

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = domain::ipc_send(&mut t, endpoint, msg_ptr);
// r = [rax, rdx]: rax is the result, or REFUSED when the call is refused
```

### `ipc_recv` (domain gate, selector 7)

**Status: wired.**

**What it does.** Copies a 16-byte message into a buffer if one is waiting.

**Why it exists.** Receive without blocking.

| register | parameter | type |
|---|---|---|
| `rdi` | `endpoint` | integer |
| `rsi` | `buf_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |

**Result.** 1 = a 16-byte message was copied, 0 = none, or REFUSED

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = domain::ipc_recv(&mut t, endpoint, buf_ptr);
// r = [rax, rdx]: rax is the result, or REFUSED when the call is refused
```

### `dma_check` (domain gate, selector 8)

**Status: wired.**

**What it does.** Checks that a buffer lies wholly inside a DMA window the domain holds.

**Why it exists.** Stops a driver programming a device to DMA outside its window.

| register | parameter | type |
|---|---|---|
| `rdi` | `addr` | integer |
| `rsi` | `len` | length or count |

**Result.** 0 if wholly inside a DMA window the domain holds, else REFUSED

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = domain::dma_check(&mut t, addr, len);
// r = [rax, rdx]: rax is the result, or REFUSED when the call is refused
```

### `irq_poll` (domain gate, selector 9)

**Status: wired.**

**What it does.** Returns the bit mask of pending granted interrupt lines and clears what it returns.

**Why it exists.** Polled interrupt handling.

Parameters: none.

**Result.** bit mask of pending granted interrupt lines; clears what it returns

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = domain::irq_poll(&mut t);
// r = [rax, rdx]: rax is the result, or REFUSED when the call is refused
```

### `grant_query` (domain gate, selector 10)

**Status: wired.**

**What it does.** Asks about the domain's own grants: kind (PCI function, MMIO, DMA, IRQ, shared window), index and field.

**Why it exists.** A domain learns what it holds from the kernel, never from constants.

| register | parameter | type |
|---|---|---|
| `rdi` | `kind` | integer |
| `rsi` | `index` | integer |
| `rdx` | `field` | integer |

**Result.** the value, or REFUSED when there is no such grant

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
// Ask for the first PCI function the host granted this domain (kind 1, index 0, field 0 = id)
let r = domain::grant_query(&mut t, 1, 0, 0);
if !refused(r[0]) { let bdf = r[0]; /* bus<<16 | dev<<8 | func */ }
```

### `port_in` (domain gate, selector 11)

**Status: wired.**

**What it does.** Reads an I/O port covered by a Port grant.

**Why it exists.** Legacy device access under a grant.

| register | parameter | type |
|---|---|---|
| `rdi` | `port` | integer |
| `rsi` | `width` | integer |

**Result.** the value, or REFUSED (no Port grant covers it)

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = domain::port_in(&mut t, port, width);
// r = [rax, rdx]: rax is the result, or REFUSED when the call is refused
```

### `port_out` (domain gate, selector 12)

**Status: wired.**

**What it does.** Writes an I/O port covered by a Port grant.

**Why it exists.** Legacy device access under a grant.

| register | parameter | type |
|---|---|---|
| `rdi` | `port` | integer |
| `rsi` | `width` | integer |
| `rdx` | `value` | integer |

**Result.** 0, or REFUSED

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = domain::port_out(&mut t, port, width, value);
// r = [rax, rdx]: rax is the result, or REFUSED when the call is refused
```

### `notif_wait` (domain gate, selector 39)

**Status: wired.**

**What it does.** Returns the badge word of the interrupt lines that fired and parks the domain until one does; re-opens the lines that fired.

**Why it exists.** Interrupt-driven drivers instead of polling.

Parameters: none.

**Result.** the badge word of the interrupt lines that fired (never 0), parking the domain until one does; or REFUSED

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = domain::notif_wait(&mut t);
// r = [rax, rdx]: rax is the result, or REFUSED when the call is refused
```

### `kernel_gate` (domain gate, selector 40)

**Status: wired.**

**What it does.** Forwards one kernel-gate call from a ring-3 driver domain through a 56-byte call block, if the host granted the domain that gate.

**Why it exists.** A user-space virtual machine monitor can use capability calls without being in the kernel.

| register | parameter | type |
|---|---|---|
| `rdi` | `block_ptr` | pointer into the caller's own memory (must lie wholly in memory the caller can access) |

**Result.** the kernel gate's status word, or REFUSED when the host did not grant the domain the gate or the 56-byte call block (selector, cptr, depth, four args) lies outside the domain; R1: a ring-3 VMM forwards capability-gate calls through this

**Example.** Using the generated Rust client (the capability argument is the address of a slot in your own capability space):

```rust
let r = domain::kernel_gate(&mut t, block_ptr);
// r = [rax, rdx]: rax is the result, or REFUSED when the call is refused
```

