# EaseFilter File Security Rust SDK

<strong>Memory-Safe Windows File System Security, EDR, and DLP in Rust</strong>

[![Platform](https://img.shields.io/badge/Platform-Windows-blue.svg)]()
[![Language](https://img.shields.io/badge/Language-Rust-success.svg)]()
[![License](https://img.shields.io/badge/License-Commercial-orange.svg)]()
![Security](https://img.shields.io/badge/Security-AES--256-red)
---

## Overview

Interacting deeply with the Windows Kernel to build **Endpoint Detection and Response (EDR)** or **Data Loss Prevention (DLP)** agents traditionally requires writing risky, unsafe C/C++ mini-filter drivers. 

The **EaseFilter File Security SDK** bridges this gap. By utilizing Rust's Foreign Function Interface (FFI) to wrap the EaseFilter API, developers can build blazingly fast, memory-safe security agents that natively intercept file system, registry, and process events without risking memory leaks or null-pointer dereferences in ring 0.

<Image alt="EaseFilter System Architecture Diagram illustrating kernel and user mode communication" caption="EaseFilter File Security Architecture" src="https://www.easefilter.com/images/file-security-rust-architecture.jpg"/>

---

## Key Features

- **Zero-Trust File Access & Ransomware Mitigation:** Strip dangerous permissions (write, delete, rename) dynamically from sensitive directories.
- **Fearless Concurrency with MPSC Channels:** Route kernel I/O interception events safely into Rust Multi-Producer, Single-Consumer channels without blocking OS threads.
- **Transparent File Encryption (DLP):** Enforce 256-bit AES encryption on disk while decrypting files in memory only for authorized, whitelisted processes.
- **Process & Registry Auditing:** Monitor process creation trees and protect critical auto-run Windows registry hives from unauthorized tampering.

---

## How It Works

Rust language demo crates for the [EaseFilter File Security SDK](https://www.easefilter.com/kb/file-security-sdk-developer-guide.htm).
Includes the following features:

* Monitoring of file/folder access

* Control of file/folder access

* Filesystem-level encryption of specific folders and files

* Monitoring of the Windows Registry

* Monitoring of specific processes

The crate `easefilter-sys` has direct bindings generated using [bindgen],
and the `easefilter` crate provides a higher level interface.

[bindgen]: https://rust-lang.github.io/rust-bindgen/

> [!NOTE]
> A **license key is required** to use this program. Contact
> EaseFilter support by e-mail for a trial key.

If you run `cargo check` or `cargo clippy`, always run them specifically with
`-p easefilter`, to avoid recompiling `easefilter-sys`.

## Examples

Multiple Rust examples that use the bindings are provided in the
`easefilter/examples/` folder. To run an example, use the command:

    cargo run --example <example name>

> [!NOTE]
> Run from an elevated prompt (administrator) since the EaseFilter driver
> requires it. Your license key is read from the `EASEFILTER_TEST_LICENSE_KEY`
> environment variable.

### File and Directory Monitoring

**`monitor_dir`** — Monitor a directory for file-system events (create, delete,
rename, write). Logs each event with user SID, account name, and process info.

    cargo run --example monitor_dir -- "C:\path\to\watch"

### File and Directory Access Control

**`control_static`** — Deny write and delete at the driver level via
`AccessFlag`. Operations are rejected at the driver level.

    cargo run --example control_static -- "C:\path\to\protect"

**`control_dynamic`** — Userspace callback code inspects every pre-operation
I/O event and dynamically denies renames and deletes.

    cargo run --example control_dynamic -- "C:\path\to\protect"

### Transparent Encryption

**`encrypt_static`** — Transparent encryption with a static AES-256 key derived
from a passphrase via Argon2id. The key is embedded in the rule at install time.

    cargo run --example encrypt_static -- "C:\path\to\protect" "my passphrase"

**`encrypt_callback`** — Transparent encryption where every encrypt/decrypt
request fires a userspace event to dynamically supply the key.

    cargo run --example encrypt_callback -- "C:\path\to\protect"

Files created in the protected directory will be encrypted at rest on the disk,
and decrypted whenever the filter rule is active.

### Process Monitoring

**`process_monitor`** — Monitor process/thread creation and termination events
for a given executable or glob pattern. This invocation monitors all
`notepad.exe` processes.

    cargo run --example process_monitor -- "C:\Windows\System32\notepad.exe"

### Process Control

**`process_control_static`** — Block a specific executable from launching at
the driver level. The following command prevents `notepad.exe` from starting:

    cargo run --example process_control_static -- "C:\Windows\System32\notepad.exe"

**`process_control_dynamic`** — Allow or deny process creation dynamically. The
following command prevents `cmd.exe` from starting when it has `rmdir` in its
arguments:

    cargo run --example process_control_dynamic -- "C:\Windows\System32\cmd.exe" "rmdir"

### Registry Monitoring

**`registry_monitor`** — Monitor registry key operations (create, delete, set
value, rename, open). Logs each event with the key path, user SID, and process
info. The key mask is a glob pattern.

    cargo run --example registry_monitor -- "*\EasefilterRust\*"

### Registry Access Control

**`registry_control_static`** — Deny specific registry operations at the driver
level via `RegControlFlag`. The key mask is a glob pattern.

    cargo run --example registry_control_static -- "*\EasefilterRust\*"

**`registry_control_dynamic`** — Userspace callback code inspects every
registry pre-operation event and dynamically denies keys whose path contains
`"deny"`. The key mask is a glob pattern.

    cargo run --example registry_control_dynamic -- "*\EasefilterRust\*"

## EaseFilter File System Filter Driver SDK Reference
| Product Name | Description |
| --- | --- |
| [Cloud File System SDK](https://www.easefilter.com/cloud/cloud-file-system-sdk.htm) | EaseFilter Cloud File System SDK Introduction. |
| [CloudTier Storage Tiering SDK](https://www.easefilter.com/cloud/storage-tiering-sdk.htm) | EaseFilter Storage Tiering Filter Driver SDK Introduction. |
| [File Monitor SDK](https://www.easefilter.com/kb/file-monitor-filter-driver-sdk.htm) | EaseFilter File Monitor Filter Driver SDK Introduction. |
| [File Control SDK](https://www.easefilter.com/kb/file-control-file-security-sdk.htm) | EaseFilter File Control Filter Driver SDK Introduction. |
| [File Encryption SDK](https://www.easefilter.com/kb/transparent-file-encryption-filter-driver-sdk.htm) | EaseFilter Transparent File Encryption Filter Driver SDK Introduction. |
| [Registry Filter SDK](https://www.easefilter.com/kb/registry-filter-drive-sdk.htm) | EaseFilter Registry Filter Driver SDK Introduction. |
| [Process Filter SDK](https://www.easefilter.com/kb/process-filter-driver-sdk.htm) | EaseFilter Process Filter Driver SDK Introduction. |
| [EaseFilter SDK Programming](https://www.easefilter.com/kb/programming.htm) | EaseFilter Filter Driver SDK Programming. |

## EaseFilter SDK Sample Projects
| Sample Project | Description |
| --- | --- |
| [CloudTier Storage Tiering Demo](https://www.easefilter.com/cloud/cloudtier-storage-tiering-demo.htm) | A HSM File System Filter Driver Demo. |
| [CloudTier S3 Tiering Demo](https://www.easefilter.com/cloud/cloudtier-s3-intelligent-tiering-demo.htm) | CloudTier S3 Intelligent Tiering Demo. |
| [Cloud File DR S3 Demo](https://www.easefilter.com/cloud/cloud-file-dr-demo.htm) | Cloud File DR S3 Demo. |
| [Amazon S3 File Explorer Demo](https://www.easefilter.com/cloud/s3-browser-demo.htm) | Amazon S3 File Explorer Demo. |
| [Auto File DRM Encryption](https://www.easefilter.com/kb/auto-file-drm-encryption-tool.htm) | Auto file encryption with DRM data embedded. |
| [Transparent File Encrypt](https://www.easefilter.com/kb/AutoFileEncryption.htm) | Transparent on access file encryption. |
| [Secure File Sharing with DRM](https://www.easefilter.com/kb/DRM_Secure_File_Sharing.htm) | Secure encrypted file sharing with digital rights management. |
| [File Monitor Example](https://www.easefilter.com/kb/file-monitor-demo.htm) | Monitor file system I/O in real time, tracking file changes. |
| [File Protector Example](https://www.easefilter.com/kb/file-protector-demo.htm) | Prevent sensitive files from being accessed by unauthorized users or processes. |
| [FolderLocker Example](https://www.easefilter.com/kb/FolderLocker.htm) | Lock file automatically in a FolderLocker. |
| [Process Monitor](https://www.easefilter.com/kb/Process-Monitor.htm) | Monitor the process creation and termination, block unauthorized process running. |
| [Registry Monitor](https://www.easefilter.com/kb/RegMon.htm) | Monitor the Registry activities, block the modification of the Registry keys. |
| [Secure Sandbox Example](https://www.easefilter.com/kb/Secure-Sandbox.htm) |A secure sandbox example, block the processes accessing the files out of the box. |
| [FileSystemWatcher Example](https://www.easefilter.com/kb/FileSystemWatcher.htm) | File system watcher, logging the file I/O events. |
| [ZeroTrust Example](https://www.easefilter.com/kb/zero-trust-file-access-control-demo.htm) | Zero trust file acc
ess control with encryption feature. |

## Filter Driver Reference

* [Understand MiniFilter Driver](https://www.easefilter.com/kb/understand-minifilter.htm)
* [Understand File I/O](https://www.easefilter.com/kb/File_IO.htm)
* [Understand I/O Request Packets(IRPs)](https://www.easefilter.com/kb/understand-irps.htm)
* [Filter Driver Developer Guide](https://www.easefilter.com/kb/DeveloperGuide.htm)
* [MiniFilter Filter Driver Framework](https://www.easefilter.com/kb/minifilter-framework.htm)
* [Isolation Filter Driver](https://www.easefilter.com/kb/Isolation_Filter_Driver.htm)

## License

This repository contains two distinct licensing categories:

1. **Wrapper/Implementation Code (MIT License):** 
   The source code, scripts, and configuration files provided directly in this repository are licensed under the [MIT License](LICENSE). You are free to modify, distribute, and use this specific integration code.

2. **EaseFilter SDK (Commercial License):**
   This project requires the EaseFilter SDK (including its binaries, filter drivers, and core libraries) to function. The EaseFilter SDK is **proprietary commercial software** and is strictly excluded from the MIT License.   
   To use the EaseFilter SDK for commercial purposes or in a production environment, you must obtain a valid commercial license directly from [EaseFilter](https://www.easefilter.com/license.pdf).

## Support
If you have questions or need help, please contact support@easefilter.com 

[Home](https://www.easefilter.com/) | [Solution](https://www.easefilter.com/solutions.htm) | [Download](https://www.easefilter.com/download.htm) | [Demos](https://www.easefilter.com/online-fileio-test.aspx) | [Blog](https://blog.easefilter.com/) | [Programming](https://www.easefilter.com/kb/programming.htm)
