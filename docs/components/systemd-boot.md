# systemd-boot — BLS reader + integration

`github.com/go-bootloaders/systemd-boot` is a pure-Go (`CGO_ENABLED=0`)
reader and integration layer for
[systemd-boot](https://www.freedesktop.org/software/systemd/man/systemd-boot.html),
following the
[Boot Loader Specification](https://uapi-group.org/specifications/specs/boot_loader_specification/).

It is a production-ready consumer of the pure-Go storage / firmware / boot
stack: point it at a **whole disk image** and it GPT-locates the EFI System
Partition, mounts its FAT32 filesystem, and returns resolved BLS boot entries
— then optionally reads the UEFI boot-variable state and measures the
selected kernel/initrd/cmdline into TPM PCRs, all with no cgo and no
shell-outs.

## What it does

| Capability | Backed by |
|---|---|
| Parse `/loader/loader.conf` + `/loader/entries/*.conf` → resolved `Boot` entries (linux/initrd/options, BLS sort order, wildcard `default`) | this package |
| Disk image → ESP → resolved entries with zero manual FS plumbing | [`go-volumes/gpt`](https://github.com/go-volumes/gpt) + [`go-filesystems/detect`](https://github.com/go-filesystems/detect) (`fat32reg`) |
| Read UEFI `BootOrder`/`Boot####` LoadOptions, set `BootNext`/`BootOrder`, find which `Boot####` is systemd-boot | [`go-filesystems/uefi`](https://github.com/go-filesystems/uefi) |
| Measured boot: extend kernel (PCR 4) / cmdline (PCR 8) / initrd (PCR 9) into a TPM + emit a TCG event log | [`go-tpm2`](https://github.com/go-tpm2) (`tpm2`, `attest`, `common`) |

## Disk-image → BLS entries

`OpenImage` does the whole pipeline — GPT-locate the ESP, detect + mount its
FAT32 filesystem, adapt it to the reader, parse `loader.conf` — behind a
single call. The caller MUST `Close` the returned `Image`.

```go
im, err := systemdboot.OpenImage("/dev/sda")     // or a disk-image file
defer im.Close()

def, _ := im.Default()        // the active entry (loader.conf default, or BLS-highest)
all, _ := im.Entries()        // every entry, newest first
```

Callers who **already** hold a mounted ESP filesystem (any `go-filesystems`
driver) skip the disk plumbing with `NewFS`:

```go
fs := systemdboot.NewFS(mountedESP)          // filesystem.Filesystem → FS
boot, _ := systemdboot.Default(fs, "/")      // espRoot within the mount
```

## UEFI boot-variable state

```go
store, _ := uefi.Open("OVMF_VARS.fd")
defer store.Close()

state, _ := systemdboot.ReadBootState(store)
// state.Order, state.Entries, state.SystemdBootSlots, state.BootNext

systemdboot.SetBootNext(store, state.SystemdBootSlots[0])  // boot sd-boot once
systemdboot.MakeSystemdBootFirst(store)                    // make it default
```

## Measured boot (optional, TPM-free by default)

```go
// nil Extender → pure event-log dry run, no TPM touched.
res, _ := systemdboot.MeasureBoot(im, def, nil, 512<<20)

// or measure into hardware over a crb/tis transport:
tpm := tpm2.New(transport)
res, _ := systemdboot.MeasureBoot(im, def, tpm, 512<<20)
```

The TPM dependency sits behind the injected `Extender` interface, so the tool
builds and runs with no TPM present.

## Why this exists

The conventional `/boot/{vmlinuz,Image}-*` glob misses **every systemd-boot
install** — NixOS, Clear Linux, and bespoke Arch/Gentoo setups put the kernel
at content-addressed `/EFI/<distro>/<hash>-*` paths selected through
`/loader/entries/*.conf`. Reading the canonical BLS path covers the whole
ecosystem at once.

## Status

Validated on all six 64-bit Go architectures (amd64, arm64, riscv64,
loong64, ppc64le, and big-endian s390x). 92.9% test coverage. All
dependencies resolve from the public Go module proxy by pseudo-version —
no `replace => ../sibling`, no local sibling clone, no vendoring. No tagged
release yet.

## License

BSD-3-Clause — see [LICENSE](https://github.com/go-bootloaders/systemd-boot/blob/main/LICENSE).
