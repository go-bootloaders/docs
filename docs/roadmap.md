# Roadmap & Status

This page is the **single source of truth** for what actually exists in the
`go-bootloaders` organization. It is deliberately conservative: a target is only
moved past *Planned* once its repository contains real, building Go code.

!!! note "Honest status, last reviewed 2026-06"
    Nothing in `go-bootloaders` has shipped. No module has a `go.mod`, a tagged
    release, or an installable artifact. The list below reflects the repositories
    as they are today, not as we hope they will be.

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
| [`grub`](https://github.com/go-bootloaders/grub) | GRUB multi-OS boot menu | **Scaffolding** | Repository created; contains only a README stub and a LICENSE — no `go.mod`, no code. |
| `limine` | Limine boot protocol (modern x86_64 / UEFI) | **Planned** | Not yet created. |
| `refind` | rEFInd graphical UEFI boot manager | **Planned** | Not yet created. |
| `syslinux` | Syslinux / ISOLINUX / PXELINUX | **Planned** | Not yet created. |
| `systemd` | systemd-boot (`sd-boot`) | **Planned** | Not yet created. |
| `u-boot` | U-Boot embedded / SoC bootloader | **Planned** | Not yet created. |

## Supporting repositories

These are not bootloaders but are part of the organization:

| Repo | What it is | State |
|------|------------|-------|
| [`brand`](https://github.com/go-bootloaders/brand) | Logos and icons (the family brand kit) | Populated |
| [`go-bootloaders.github.io`](https://github.com/go-bootloaders/go-bootloaders.github.io) | Organization landing page | Populated |
| [`docs`](https://github.com/go-bootloaders/docs) | This documentation site (MkDocs Material + mike) | Populated |

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

When a target reaches *In progress* or *Released*, it gets its own component
page here, linked from the navigation. Until then, this roadmap is the whole
story.

## Contributing

The honest answer is that the project is at the very beginning. The most useful
contributions right now are design discussion and prototype code in the target
repositories. Track progress through the
[organization](https://github.com/go-bootloaders) directly.
