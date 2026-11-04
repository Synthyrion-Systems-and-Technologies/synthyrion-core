# Your first program

This chapter walks from an empty machine to a program that calls the kernel. It is honest about the point where the released kernel stops and your own loader begins.

## 1. What you need

* A Rust toolchain with the bare-metal x86-64 UEFI target: `rustup target add x86_64-unknown-uefi`.
* QEMU for x86-64 and the OVMF UEFI firmware (any recent QEMU; the project's own runs use QEMU on Linux with KVM and on Windows with the Windows Hypervisor Platform or plain emulation).
* The download: the kernel and root task for x86-64 with its source, tests and the checking evidence that exists today, a signed licence file and a checksum with a signature. Verify the SHA-512 and the ML-DSA-87 signature against the key digest published on the download page before you build.

## 2. Build the boot image

The boot application is a `no_std` crate whose default target is `x86_64-unknown-uefi`, so inside its directory a plain release build produces the UEFI executable:

```
cargo build --release
```

Features select what the image contains. The default image is deliberately small and bounded: it boots, runs its start-up checks and finishes. Feature builds add demonstration scenes (for example one in which a user-mode program calls the kernel gate) and the interactive desktop session; they are off by default, because a loop that never returns is wrong for a script that boots an image and waits for a marker.

## 3. Run it under QEMU

A UEFI machine boots `EFI/BOOT/BOOTX64.EFI` from a FAT volume. QEMU can present a directory as one. This is an example command line; adjust memory, machine type and firmware path to your host:

```
mkdir -p esp/EFI/BOOT
cp <the built .efi> esp/EFI/BOOT/BOOTX64.EFI
qemu-system-x86_64 -machine q35 -m 512 -smp 2 \
  -drive if=pflash,format=raw,readonly=on,file=OVMF_CODE.fd \
  -drive format=raw,file=fat:rw:esp \
  -serial stdio -display none
```

The kernel reports on the serial line. The lines are plain text: scene names and PASS or FAIL checks. Multi-core is selected with `-smp` (2, 4 and 8 cores are exercised in emulation). A VM is emulation evidence; do not read a QEMU result as a hardware result.

## 4. What the first program looks like

The first user program the kernel runs is the **root task**. It starts with the capability layout in [Boot and platforms](boot_platforms.md), section 4. Read its boot information first, then build your system from the untyped memory it was given. In outline, with the generated Rust client (`t` is the transport that performs the trap):

```rust
// 1. Make a CNode with 2^4 = 16 slots out of the first untyped capability (slot 5), into slot 10 of the root CNode (slot 2)
let r = kernel::retype(&mut t, /*untyped*/ 5, RETYPE_KIND_CNODE_FLAG | 4, 1, /*dest cnode*/ 2, /*first slot*/ 10);
assert_eq!(r[0], status::OK);

// 2. Make a notification and an endpoint-bearing task from the same memory
kernel::retype(&mut t, 5, RETYPE_KIND_NOTIFICATION_FLAG, 1, 2, 11);
kernel::retype(&mut t, 5, RETYPE_KIND_TASK_FLAG, 1, 2, 12);

// 3. Give the new task its capability space, then release it
kernel::task_set_cspace(&mut t, /*schedule authority*/ 3, /*task*/ 12, /*cnode*/ 10);
kernel::task_admit(&mut t, /*spawn authority*/ 4, /*task*/ 12, /*confined*/ 1);
```

A new task is born parked. It also needs an address space: `tcb_bind_vspace` places a fresh task in the address space of a donor task, and the donor must itself be a retyped task whose space has a page-table tree (a donor that runs in the kernel space is `INVALID`). The slot numbers above are only an outline; the real ones come from the boot information.

Check every `r[0]`. A refused call changes nothing, so the usual failures are rights or slot addresses: `LOOKUP` means the address did not resolve, `DENIED` means the capability was the wrong kind or lacked a right, `ALREADY_MAPPED` means the destination slot was not empty.

## 5. A client and a server

The smallest useful system is two tasks and an endpoint. The root task makes the endpoint, mints a badged copy for each client with `cnode_mint`, and places the server's receive capability in the server's CNode. The server runs:

```rust
let mut rec = [0u64; 7];
let mut answer = 0;
loop {
    let r = kernel::reply_recv(&mut t, my_ep, answer, rec.as_mut_ptr() as u64);
    // rec[0] is the kind (2 = call), rec[1] the sender, rec[2] the message word, rec[3] the badge
    answer = handle(rec[2], rec[3]);
}
```

(The first iteration has no call to answer; the server begins with a plain `receive`.) A client:

```rust
let r = kernel::call(&mut t, server_ep, 42, 0, 0, 0);
```

## 6. Where the released kernel stops

At the last status measurement, the root task's address space and entry are supplied by the architecture layer's loader, and the released kernel does not include a general loader for user program binaries: user-mode programs that call the kernel gate run on the booted image through the gate demonstration build, and driver domains through the domain host. Building your own loader that places a root-task image and its boot information is the integration step the kernel leaves to you. If you need it sooner, ask through the contact page.

## 7. Check your work

* The generated reference tells you for each call which capability and rights it needs.
* [Errors and faults](errors_faults.md) tells you what a refusal means.
* Run the kernel's own test suite on a development machine to see every call exercised: the tests are part of the download.
