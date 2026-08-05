# Roadmap & Status

This page is the **single source of truth** for what actually exists in the
`go-bootloaders` organization. It is deliberately conservative: a target is only
moved past *Planned* once its repository contains real, building Go code.

!!! note "Honest status, last reviewed 2026-08-05"
    `grub` and `systemd-boot` are real, tested Go modules — both build with
    `CGO_ENABLED=0`, pass their test suites on all six 64-bit Go architectures,
    and are already consumed downstream (e.g.
    [`go-diskimages/diskimage`](https://github.com/go-diskimages/diskimage)
    imports `grub`). Neither has cut a tagged release yet, so — by the letter
    of the status legend below — both sit at **In progress** rather than
    **Released**; in practice both are usable today via Go pseudo-version
    imports, exactly as any untagged module is.

## Status legend

| State | Meaning |
|-------|---------|
| **Planned** | Named in the roadmap; repository does not yet exist or holds no code. |
| **Scaffolding** | Repository exists with placeholder files (README, LICENSE) but no Go code. |
| **In progress** | Real Go code is landing; no tagged release yet. |
| **Released** | A tagged release exists and the module is importable. |

## Target bootloaders

| Target | Models | State | Notes |
|--------|--------|-------|-------|
| [`grub`](https://github.com/go-bootloaders/grub) | GRUB multi-OS boot menu | **In progress** | `github.com/go-bootloaders/grub`, 93.9% test coverage. Opens a GPT disk image, mounts the ESP (FAT32) and a Linux `/boot` (ext4/btrfs) read-write, reads/generates/patches `grub.cfg` on both, registers a UEFI boot entry, and optionally measures `grub.cfg` + kernels/initrds into TPM PCRs. No tagged release yet. |
| [`systemd-boot`](https://github.com/go-bootloaders/systemd-boot) | systemd-boot (`sd-boot`), Boot Loader Specification | **In progress** | `github.com/go-bootloaders/systemd-boot`, 92.9% test coverage. GPT-locates the ESP, parses `loader.conf` + BLS `entries/*.conf` into resolved boot entries, reads/writes UEFI `BootOrder`/`BootNext`, and measures the selected kernel/initrd/cmdline into TPM PCRs with a TCG event log. No tagged release yet. |
| `limine` | Limine boot protocol (modern x86_64 / UEFI) | **Planned** | Not yet created. |
| `refind` | rEFInd graphical UEFI boot manager | **Planned** | Not yet created. |
| `syslinux` | Syslinux / ISOLINUX / PXELINUX | **Planned** | Not yet created. |
| `u-boot` | U-Boot embedded / SoC bootloader | **Planned** | Not yet created. |

## Supporting repositories

These are not bootloaders but are part of the organization:

| Repo | What it is | State |
|------|------------|-------|
| [`brand`](https://github.com/go-bootloaders/brand) | Logos and icons (the family brand kit) | Populated |
| [`go-bootloaders.github.io`](https://github.com/go-bootloaders/go-bootloaders.github.io) | Organization landing page | Populated |
| [`docs`](https://github.com/go-bootloaders/docs) | This documentation site (MkDocs Material + mike) | Populated |

`go-bootloaders` does not maintain its own `interface` repo; both `grub` and
`systemd-boot` consume the shared filesystem/partition contracts from
[`go-filesystems/interface`](https://github.com/go-filesystems/interface) and
[`go-volumes/gpt`](https://github.com/go-volumes/gpt) as ordinary pseudo-version
dependencies.

## What "done" will look like

A target graduates from *Planned* → *Released* when it can, from source with
`CGO_ENABLED=0`:

1. **Parse its native configuration** (e.g. `grub.cfg`, `loader.conf`,
   `extlinux.conf`) into a typed model.
2. **Probe partitions and filesystems** enough to locate kernels and initrds.
3. **Load a kernel and initrd** and present the appropriate boot menu.
4. **Hand off** to the loaded kernel via the right protocol (Multiboot2,
   Linux boot protocol, EFI stub, …).
5. Ship with tests and a tagged release.

`grub` and `systemd-boot` satisfy 1–3 today (config parsing, filesystem/partition
probing, and kernel/initrd discovery); actual kernel handoff (4) is out of scope
for both — they operate on **disk images** ahead of boot (preparing, patching,
inspecting, and measuring what a real firmware/bootloader will later load), not
as a running bootloader stage themselves. Only step 5 — a tagged release — is
outstanding for both before they'd count as *Released* by this page's own bar.

## Contributing

`grub` and `systemd-boot` are in active use; issues and PRs against them follow
the normal Go module workflow (tests, `CGO_ENABLED=0`, all six 64-bit
architectures). For the four remaining *Planned* targets, the most useful
contributions right now are design discussion and prototype code in the target
repositories. Track progress through the
[organization](https://github.com/go-bootloaders) directly.
