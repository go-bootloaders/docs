# go-bootloaders

Pure-Go **bootloaders** — an effort to reimplement the classic boot stack in Go,
with no cgo.

!!! warning "Early stage — nothing has shipped yet"
    `go-bootloaders` is at the **planning and scaffolding** stage. There is no
    released code, no installable artifact, and no API to depend on. This site
    documents the **intent and roadmap**, not shipped software. Treat every
    component below as *planned* until a repository has real code and a tagged
    release. See [Roadmap & Status](roadmap.md) for the honest state of each
    repo.

## The idea

A bootloader is one of the last pieces of low-level system software still
written almost exclusively in C and assembly. `go-bootloaders` asks whether the
boot stack — config parsing, filesystem and partition probing, kernel/initrd
loading, the boot menu and the chainload/handoff to the kernel — can be expressed
in **memory-safe Go**, built from source with `cgo` disabled.

The aim is a family of independent modules, each one a Go-native take on a
well-known bootloader, sharing as much common machinery (config parsing,
filesystem readers, ELF/PE loading, EFI plumbing) as is sensible.

## Planned family

The organization's [brand assets](https://github.com/go-bootloaders/brand)
enumerate the six target bootloaders. Each is a **placeholder** today; see
[Roadmap & Status](roadmap.md) for the actual repository state.

| Target | Models | Status |
|--------|--------|--------|
| `grub` | GRUB-style multi-OS boot menu | Scaffolding (README/LICENSE only) |
| `limine` | Limine modern x86_64/UEFI boot protocol | Planned |
| `refind` | rEFInd-style graphical UEFI boot manager | Planned |
| `syslinux` | Syslinux/ISOLINUX/PXELINUX family | Planned |
| `systemd` | systemd-boot (`sd-boot`) UEFI stub/menu | Planned |
| `u-boot` | U-Boot embedded/SoC bootloader | Planned |

## Where things actually stand

Nothing is shippable yet. The only non-fork repositories that exist are an empty
`grub` placeholder (a README stub and a license, no Go code), the `brand`
asset repository, the org landing site, and this `docs` site. The full,
honest accounting is on the [Roadmap & Status](roadmap.md) page.
