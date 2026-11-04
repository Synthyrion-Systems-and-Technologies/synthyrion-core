# Limits

Every table in the kernel has a fixed capacity. A request past a capacity is refused with a named status, the refusal is counted per table (so an exhausted table is visible as an event), and nothing grows without bound or panics. The numbers below are read from the kernel's code and may change in a later release; the append-only call table will not.

## Tasks, capabilities and objects

| limit | value | what happens past it |
|---|---|---|
| live tasks | 4,096 | the spawn is refused (a task that exits frees a row) |
| capability slots over all CNodes | 1,048,576 (4,096 tasks times the largest CNode of 256 slots) | `INVALID`, counted |
| slots in one CNode | 2 to 256 (a power of two, at most 8 bits) | the retype is `INVALID` |
| objects made by one retype | 16 | `INVALID` |
| capabilities carried by one message | 3 | `INVALID` |
| outstanding sends | 32 | the send is refused |
| badged endpoints | 256 | refused, counted |
| signal slots | 256 | refused, counted |
| revocation-epoch slots | 20,808 in all (4,096 each for task and endpoint ids, notification ids, reply ids, kernel objects, and badged-endpoint, signal, VCPU and EPT ids; 64 scheduling contexts; 264 fixed special objects and device or interrupt-line ids) | refused at creation, counted, never failing open |
| published-but-unattached shares | 1,024 | `WOULD_BLOCK` |

## Scheduling

| limit | value |
|---|---|
| priority range | 0 to 255 |
| partition windows in one schedule | 256 |
| scheduling contexts | 64 |

## Memory and address spaces

| limit | value | status past it |
|---|---|---|
| ASID pools | 64 | `EXHAUSTED` |
| PCIDs | 4,095 shared among live pools (1 to 4,095 per pool, disjoint) | `EXHAUSTED` |
| address spaces assigned to pools | 1,024 | `EXHAUSTED` |
| pending TLB invalidations | 256 | |
| ASID control objects | 1 | a second is `INVALID` |

## Devices

| limit | value | status past it |
|---|---|---|
| IO spaces (DMA address spaces) | 16 | `INVALID` |
| devices bound to IO spaces | 256 | `EXHAUSTED` |
| mappings held in IO spaces | 4,096 | `EXHAUSTED` |
| device address range | below 2^48, 4 KiB aligned | `INVALID` |
| I/O-port ranges | 64 | `EXHAUSTED` |
| interrupt lines | 256 device or line ids | `INVALID` |

## Virtual machines

| limit | value |
|---|---|
| VCPUs | 16 |
| guest-physical spaces (EPT/NPT) | 16 |
| protected host ranges | 16 |
| register image | 18 words |
| vector, event kind and instruction length | 0 to 255 |

## Rings

| limit | value |
|---|---|
| submissions served by one `ring_enter` | 32 |
| ring size | submission and completion queue sizes are powers of two up to 2^8 entries, completion queue at least as large as the submission queue |
| submission entry | 64 bytes; completion entry 32 bytes; header 64 bytes |

## Driver domains

| limit | value |
|---|---|
| message size | 16 bytes |
| endpoints | 0 to 31 |
| ring depth between domains | 64 messages |
| log write | 200 bytes |
| time without yielding (BIOS-boot image) | 4 timer ticks, about 220 ms |
| domain runtime heap | 192 KiB |

## Not limits of the kernel

File systems, network stacks and drivers are outside the kernel and have their own limits.
