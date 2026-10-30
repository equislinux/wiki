# X Linux for aarch64 (x-arm)

> Other languages: [Español](../es/index.md)

This is the documentation index for the `equislinux/x-arm` repository: the
**aarch64 port of X**, built as an overlay over the pinned x86_64 source. The
companion repositories are [`x-arm-repo`](https://github.com/equislinux/x-arm-repo)
(the `[x]/aarch64` pacman repository) and
[`scripts-arm`](https://github.com/equislinux/scripts-arm) (the ARM delta for
the generations engine).

## Overview

X Linux on ARM does **not** fork `equislinux/x`. `x-arm` pins `x` to a commit
(`vendor/x.sha`) and applies an ARM-only overlay on top of it. The x86_64
tree, the WSL images and everything else stay untouched by construction: an
ARM change can never leak into `main` of `x` by accident.

The port targets **aarch64 only** (UEFI, GRUB `arm64-efi`), based on
**Arch Linux ARM** (`core`, `extra`, `alarm`, `aur`). Out of scope for v1:
armv7h, Raspberry Pi, Asahi, Secure Boot, ARM WSL.

It produces:

- a **UEFI-only live ISO** (`x-<version>-aarch64.iso`), published on
  SourceForge under `equislinux/arm/iso`;
- the **`[x]/aarch64` repository** (signed), served from
  `https://equislinux.github.io/x-arm-repo/repo/aarch64`;
- **VM images** for Apple Silicon (UTM/Parallels/VMware) and generic `virt`
  hosts.

## Status

| Area | State |
|---|---|
| archiso profile (UEFI/GRUB, `arm64-efi`, `/boot/Image`) | done |
| `packages.aarch64` against Arch Linux ARM | done |
| Docker builder (Arch Linux ARM rootfs) | done |
| `[x]/aarch64` repository (signed, GitHub Pages) | done |
| Installer (UEFI layout, ALARM keyring/mirrors, GRUB initrd) | done |
| Desktop (Hyprland + equisdots) | works in VMs with software rendering (llvmpipe); niri untested |
| Generations (`x gen`) | via `x-scripts-arm` (see `scripts-arm`); best-effort otherwise |
| Automated e2e | not yet (validated manually in QEMU/UTM) |
| Secure Boot, Raspberry Pi, Asahi, ARMv7 | out of scope |

## Layout

```
vendor/x.sha            pinned commit of equislinux/x
vendor/archiso.sha      pinned commit of archlinux/archiso
overlay/                the ARM-only delta (profile, packages, GRUB, patches)
docker/                 Arch Linux ARM builder container
scripts/                fetch, overlay, repo build, ISO build, VM, publish
docs/                   build docs (es/en) and acceptance criteria
```

## Building

```bash
docker/build-image.sh                       # one-time: builder container
docker/run.sh scripts/fetch-x.sh            # resolve inputs
docker/run.sh scripts/fetch-archiso.sh
docker/run.sh scripts/apply-overlay.sh      # overlay + checks
docker/run.sh scripts/build-repo.sh         # [x]/aarch64 (--sign for release)
docker/run.sh sudo scripts/build-iso.sh     # live ISO
```

See [VM testing](vm-testing.md) for running the ISO or an installed image on
Apple Silicon.

## Key facts

- `archiso` is not packaged in ALARM; the pinned upstream tree is used
  (it supports `BOOTAA64.EFI`, and `x-arm` patches the `/boot/Image` kernel
  copy and the per-platform GRUB module list).
- `linux-aarch64` installs `/boot/Image(.gz)` and no `pkgbase` file; the
  installer and the generations delta (`scripts-arm`) handle both.
- ALARM databases are unsigned (packages are signed):
  `SigLevel = Required DatabaseOptional`.
- Publishing is manual: `scripts/publish-repo.sh` (x-arm-repo Pages) and
  `scripts/publish-arm.sh` (SourceForge) require the project GPG key and the
  SourceForge SSH key.
