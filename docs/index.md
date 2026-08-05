# go-bootloaders

Pure-Go **bootloader tooling** — an effort to reimplement the classic boot
stack in Go, with no cgo.

!!! note "Two targets shipped, four still on the roadmap"
    [`grub`](components/grub.md) and [`systemd-boot`](components/systemd-boot.md)
    are real, tested Go modules in production use — both are consumed by
    [`go-diskimages/diskimage`](https://go-diskimages.github.io/docs/) today.
    `limine`, `refind`, `syslinux`, and `u-boot` remain **planned**: named in
    the roadmap, not yet started. See [Roadmap & Status](roadmap.md) for the
    honest state of each repo.

## The idea

A bootloader is one of the last pieces of low-level system software still
written almost exclusively in C and assembly. `go-bootloaders` asks whether the
boot stack — config parsing, filesystem and partition probing, kernel/initrd
loading, the boot menu and the chainload/handoff to the kernel — can be expressed
in **memory-safe Go**, built from source with `cgo` disabled.

The aim is a family of independent modules, each one a Go-native take on a
well-known bootloader, sharing as much common machinery as is sensible: they
compose with the sibling [go-volumes](https://github.com/go-volumes) (GPT),
[go-filesystems](https://github.com/go-filesystems) (FAT32/ext4/btrfs/UEFI
vars), and [go-tpm2](https://github.com/go-tpm2) (measured boot) libraries
rather than reimplementing that machinery per bootloader.

## Family status

| Target | Models | Status |
|--------|--------|--------|
| [`grub`](components/grub.md) | GRUB-style multi-OS boot menu | **Shipped** — production consumer of the storage/firmware/TPM stack, 93.9% test coverage |
| [`systemd-boot`](components/systemd-boot.md) | systemd-boot (`sd-boot`) UEFI stub/menu, Boot Loader Specification | **Shipped** — 92.9% test coverage |
| `limine` | Limine modern x86_64/UEFI boot protocol | Planned |
| `refind` | rEFInd-style graphical UEFI boot manager | Planned |
| `syslinux` | Syslinux/ISOLINUX/PXELINUX family | Planned |
| `u-boot` | U-Boot embedded/SoC bootloader | Planned |

Neither `grub` nor `systemd-boot` has cut a tagged release yet; both are
consumed by downstream modules today via Go pseudo-versions (no `replace`
directives, no vendoring). See [Roadmap & Status](roadmap.md) for exact
commit/coverage figures and what remains before a v1 tag.
