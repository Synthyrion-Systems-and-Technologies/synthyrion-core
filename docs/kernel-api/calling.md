# Calling the kernel

This chapter is the convention every entry of the call reference relies on: the two gates, which registers carry what, how a capability is named, what comes back, how the interface is versioned, and the generated clients.

## 1. The two gates

| gate | entered with | for | state |
|---|---|---|---|
| kernel gate | the `syscall` instruction on x86-64 (there is deliberately no second software-interrupt vector for it: a second entry is a second surface to check) | applications and services; every call takes a capability | the dispatcher serves the rows marked wired; every other selector answers `NOT_WIRED` and changes nothing |
| driver-domain gate | `int 0x81` | ring-3 driver domains | wired and stable; 13 calls |

The ABI version is **2.0**. A row may only be added within a major version. A change to an existing row's selector, name, argument order or meaning is a new major version.

## 2. Kernel gate registers (ABI 2.0)

| register | on entry | on return |
|---|---|---|
| `rax` | selector | status word |
| `rdi` | address (`cptr`) of the presented capability in the caller's own capability space; 0 = none | scrubbed |
| `r9` | depth: number of address bits to resolve, 1 to 64 | scrubbed |
| `rsi`, `rdx`, `r10`, `r8` | arguments 1 to 4, in the order the reference lists them | `rdx` = the value, if the call returns one; the others scrubbed |

The kernel scrubs every other register before returning to user mode. A call has at most four arguments besides the capability.

### Capability addresses

Sealed capabilities never leave the kernel. A program does not hold capability bytes; it holds **slots**. A capability argument is a `cptr`: the address of a slot in the caller's own capability space, interpreted by walking the caller's tree of CNodes, consuming `depth` bits of the address from the most significant end. Every capability-valued argument of one call (the presented one in `rdi` and any others named in arguments) is resolved at the same depth in `r9`. If the address does not resolve (no root, a guard that does not match) or names an empty slot, the call returns `LOOKUP` and changes nothing. A capability a call creates is installed in a slot the caller names; the slot must be empty or the call answers `ALREADY_MAPPED`.

### Pointers

An argument of type pointer is an address in the caller's own memory. The kernel reaches it by walking the caller's page tables: only canonical lower-half user addresses, every page validated (user-accessible, and writable for an output) before a byte moves. A pointer outside memory the caller can access makes the call `INVALID` with no effect.

### Records

Calls that read or write a record use little-endian 64-bit words in the caller's memory. The reference gives the word layout for each: for example the receive record is seven words, `cnode_lookup` writes five, and the register images used for tasks and VCPUs are 18 words.

## 3. Results

The status word is in `rax`; a value, if the call has one, is in `rdx`. [Status words](api_constants.md) lists them all with meanings. The rules that hold for every call:

1. **A refused call changes nothing.** Rights and kinds are checked before anything happens.
2. **The capability is checked first**, then the arguments.
3. **No call blocks except where its entry says so**: `call`, `wait` and the blocking receive paths. A call that cannot proceed otherwise returns `WOULD_BLOCK`.

## 4. Driver-domain gate registers

`rax` = selector, `rdi`, `rsi`, `rdx`, `r10` = arguments, result in `rax`; every other register is preserved. A refused call returns `0xFFFFFFFFFFFFFFFF` (`REFUSED`), and the kernel records the reason as one of the named refusals (see [Errors and faults](errors_faults.md)).

## 5. The one table, and the generated clients

Every call number, argument list and wire convention is declared in one definition: a table with one row per call giving the gate, the stable selector, the name, the ABI minor version it first appeared in, the capability it presents, its arguments in register order and its result. Nothing else is allowed to be the authority for a number.

* A **Rust client** (a `no_std` crate with no unsafe code) is generated from the table: constants per gate (`sel_kernel::SEND`, `sel_domain::PORT_IN`) and one typed function per call that builds the register image and hands it to a **transport**. The transport is what actually performs the trap; it lives with its host. The examples in this documentation use that client: `kernel::name(&mut t, cap, args...)` and `domain::name(&mut t, args...)`, each returning `[rax, rdx]`. `yield` is `yield_now`.
* A **C header** is generated from the same table: version macros, one macro per selector, the status words, a table macro listing every call, and the prototype of the one invoke function a binding supplies (gate vector, selector, five argument words; it returns `rax` and `rdx`).
* A **drift test** regenerates both and fails on any difference, so a number edited in one place and not the other turns a check red. An **append-only golden list** pins every row: a row never changes selector or name and never disappears.

The table also gives the numbers of the status words, the capability kinds, the retype kind words and the ring layout constants; they are in the [constants](api_constants.md) chapter.

## 6. Guest link

When the kernel runs as a guest, a framed protocol over a serial channel lets a host client enumerate the guest's call table and send signed commands: frames with a magic, a wire version, kind, sequence number, length (at most 1024) and a CRC-32. The messages are hello and acknowledge (carrying the ABI version and a table identity, a major mismatch is refused), ping, get-ABI, get-call, and a signed command envelope handed unchanged to the guest's command sink. The CRC detects corruption; the signature inside the envelope is the only authority. The guest-side driver that feeds it from a virtual serial queue does not exist yet.

## 7. What "wired" means

A row is **wired** when the dispatcher serves it and a test pins the list of wired selectors equal to the dispatcher's. That is a statement about the table and the dispatcher. How far a wired call has been exercised on a booted image, from user mode, and on which platform is stated on the Features page (row "syscall ABI"): a subset of the wired rows has been run from user mode on a booted x86-64 image under emulation, the rest are tested at host level. A **defined** row has its selector and name fixed and no handler yet. A **superseded** row is kept forever but a later row replaces it.
