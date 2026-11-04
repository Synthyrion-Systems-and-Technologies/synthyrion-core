# Status words

Every kernel-gate call returns a status word in `rax` and, when there is a value, the value in `rdx`. A refused call changes nothing. The domain gate does not use these words: it returns the result in `rax` or `REFUSED` (`0xFFFFFFFFFFFFFFFF`).

| word | value | meaning |
|---|---|---|
| `OK` | 0 | The call succeeded. The value, if any, is in `rdx`. |
| `DENIED` | 1 | The capability is genuine but of the wrong kind, lacks a required right, or the authority needed is not held. Nothing changed. |
| `INVALID` | 2 | An argument is out of range, an object is in the wrong state, or the target does not exist. Nothing changed. |
| `WOULD_BLOCK` | 3 | The call would have to wait (an empty receive, a task that is already blocked). Also the resume value of a task that blocks. |
| `BOUNCED` | 4 | The message was returned to the sender (for example a receiver that cannot take it); the word is in the value. |
| `EXHAUSTED` | 5 | A table or pool is full: no free PCID range, no free device or mapping slot, or the region is used up. |
| `ALREADY_MAPPED` | 6 | The destination slot, address or device address is already occupied. Nothing was overwritten. |
| `WRITE_EXECUTE` | 7 | The request would make memory both writable and executable, or would write a frozen frame. |
| `HITS_PROTECTED` | 8 | A guest mapping would land on host memory declared protected. |
| `UNMAPPED` | 9 | Nothing is mapped at that address. |
| `PROTECTION` | 10 | The access is mapped but the mapping's rights forbid it. |
| `NO_IOMMU` | 14 | No IOMMU backend is attached to the kernel. |
| `BUSY` | 15 | The device is already in a space, or the port range overlaps a live one. |
| `HARDWARE` | 16 | The IOMMU did not answer or refused; the kernel's record is kept. |
| `NOT_WIRED` | 17 | The selector is not served by the kernel gate (defined but not wired, superseded, or no such row). Nothing changed. |
| `LOOKUP` | 18 | The capability address did not resolve in the caller's own capability space (no root, a guard that does not match) or names an empty slot. |
| `RING_FULL` | 19 | A ring completion queue is full; nothing was consumed. |
| `CANCELED` | 20 | A ring completion: an earlier entry of its chain failed, so this one was not served. |
| `NOT_RINGABLE` | 21 | A ring completion: this selector cannot be carried by a ring (it can block, yield, retire the caller or open a ring). |

Status values 11, 12 and 13 name conditions that are reported through other words (`WRITE_EXECUTE` for an overlap with frozen code, and the cache-maintenance range errors) and are not reused.

## Capability kinds

| kind | value |
|---|---|
| `MEMORY` | 1 |
| `ENDPOINT` | 2 |
| `DEVICE` | 3 |
| `VAULT` | 4 |
| `TASK` | 5 |
| `NOTIFICATION` | 6 |

## VCPU states

| state | value |
|---|---|
| `STOPPED` | 0 |
| `RUNNABLE` | 1 |
| `RUNNING` | 2 |
| `WAITING` | 3 |
| `DEAD` | 4 |

## Retype kind words

The `kind` argument of `retype` (selector 48): `0` = frame; `n` (1 to 4294967295) = untyped memory of n frames; the flag words below are ORed with a count or width.

| flag | bit | meaning |
|---|---|---|
| `RETYPE_KIND_CNODE_FLAG` | 1 << 32 | CNode; low bits = bits of slots (1 to 8) |
| `RETYPE_KIND_LARGE_FRAME_FLAG` | 1 << 33 | large frame; low bits 1 = 2 MiB, 2 = 1 GiB |
| `RETYPE_KIND_ASID_POOL_FLAG` | 1 << 34 | ASID pool: refused by retype (INVALID); make pools with asid_pool_make |
| `RETYPE_KIND_TASK_FLAG` | 1 << 35 | task object (born parked and confined, charged one frame) |
| `RETYPE_KIND_NOTIFICATION_FLAG` | 1 << 36 | notification object (charged one frame) |
| `RETYPE_KIND_VCPU_FLAG` | 1 << 37 | VCPU; low bits: 0 = Intel VT-x, 1 = AMD-V |
| `RETYPE_KIND_EPT_FLAG` | 1 << 38 | reserved for EPT/NPT spaces; answers INVALID today |
| `RETYPE_KIND_ASID_CONTROL_FLAG` | 1 << 39 | the ASID control (at most one per kernel) |

## EPT permission bits

`EPT_R` = 1, `EPT_W` = 2, `EPT_X` = 4. Write plus execute is never allowed.

## Ring layout constants

| name | value |
|---|---|
| `MAX_BITS` | 8 |
| `ENTER_BUDGET` | 32 |
| `HDR_BYTES` | 64 |
| `SQE_BYTES` | 64 |
| `CQE_BYTES` | 32 |
| `MAGIC` | 0x474e_4952_4e4f_4941 |
| `VERSION` | 1 |
| `H_SQ_ENTRIES` | 16 |
| `H_CQ_ENTRIES` | 24 |
| `H_SQ_TAIL` | 32 |
| `H_SQ_HEAD` | 40 |
| `H_CQ_HEAD` | 48 |
| `H_CQ_TAIL` | 56 |
| `FLAG_LINK` | 1 |
| `IN_FLIGHT` | 0xFFFFFFFFFFFFFFFF |
