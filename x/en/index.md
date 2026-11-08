# X distribution repository

> Other languages: [Español](../es/index.md)

This is the documentation index for the `x` repository (`equislinux/x`), the home
of the **X** distribution (formerly known as *X Linux* / `x-linux`). X is a
custom Arch Linux spin focused on simplicity, clean branding, and reproducible
builds. It ships its own package repository (`x-repo`) so X-specific packages
can be installed directly with `pacman`.

## Overview

The repository contains the distribution build source, not a user-facing
product on its own. It provides:

- An **archiso profile** that produces the bootable **live ISO**.
- A **text installer** that runs automatically in the live environment
  (no graphical installer; Calamares was removed).
- **Provisioning** of the installed system through the `x-scripts` payload,
  shipped offline inside the ISO.
- Helpers to build **WSL-importable root filesystem tarballs**.

The ISO uses the standard `mkarchiso` workflow. A custom `[x]` pacman
repository is declared in `pacman.conf` and is used both at build time and on
the installed system, so X branding and tooling packages (`x-release`,
`x-dev`) install with `pacman`.

## Project status

The distribution is under active development. Signed ISOs are published on
SourceForge (the stable link is
<https://equislinux.github.io/web/download/latest/>), and both package
channels — the pacman `[x]` repository and the native `.xp` endpoint — are
live. The text installer offers Hyprland, niri, KDE Plasma or a console-only
system. See `ROADMAP.md` and `docs/project-state.md`.

## How this repository maps to the organization

Each repository lives in its own folder under the `equislinux` organization on
GitHub. Repo names changed over time, so older references to `x-linux` (the
distro) or to an `x` scripts repo must be read with this mapping in mind:

| Repository | Role |
|------------|------|
| `equislinux/x` | **The distro (this repo).** archiso profile, live ISO, text installer, WSL rootfs builds. |
| `equislinux/scripts` | Provisioning payload and the `x` CLI (`x setup`, `x theme`, ...). Packaged as `x-scripts` and installed by the text installer. |
| `equislinux/x-repo` | X binary package repository (hosted on GitHub Pages, `[x]` in `pacman.conf`) plus the package portal. |
| `equislinux/xpm` | X package manager (Rust). |
| `equislinux/xpkg` | X packaging tool for developers (Rust). |
| `equislinux/wsl` / `equislinux/wsl-scripts` | WSL rootfs (published release `v0.1.0`) and its first-run setup. |
| `equislinux/wiki` / `equislinux/web` | Documentation sources (en/es) and the documentation portal. |
| `equisdots/*` | Org that owns the desktop stacks (Hyprland Lua config, niri config, Quickshell shell, palettes, engines, SDDM login) consumed read-only by the desktop setup tools in `scripts`. Official installers: `equisdots/dots` (Hyprland) and `equisdots/niri-meta` (niri). |

Related documentation in this repository:

- [Build the ISO and WSL rootfs](building.md)
- [Text installer](installer.md)
- [Generations (btrfs snapshots, rollback, restore)](generations.md)
- [Testing in a VM](vm-testing.md)
- [Project layout reference](project-layout.md)

## Highlights

- **Branding.** Identity applied to GRUB (`GRUB_DISTRIBUTOR="X"`), the ISO
  label/publisher, MOTD, and installed packages.
- **Text installer.** `configurator.sh` collects options and writes a JSON
  plan; `install.sh` partitions (GPT/btrfs), runs `pacstrap`, configures the
  base system, provisions with `x-scripts`, and installs a bootloader.
- **Unattended install.** The ISO ships an `autoinstall` boot entry (hotkey
  `a`, `xauto=1`) for a disk labeled `cidata` containing `x-install.json`.
- **System generations.** The installer creates `@`/`@home`/`@snapshots`/
  `@xstate` btrfs subvolumes and a bootable first generation; the payload
  records bootable snapshots with manifests, rollback and granular restore
  (`x gen`), plus dotfile generations (`x home`) and pacman hooks. See
  [Generations](generations.md).
- **Desktop choice.** Hyprland (`equisdots/dots`), niri
  (`equisdots/niri-meta`) or KDE Plasma with X branding; a console-only
  install is also available. The choice is made in the text installer.
- **Provisioning payload.** The `x-scripts` package ships inside the ISO. The
  Hyprland snapshot remains available for legacy offline installs; the new
  selector installs Hyprland, niri and KDE Plasma online.
- **WSL support.** The rootfs is built in the dedicated `equislinux/wsl` repository
  (release `v0.1.0`) and provisioned with `equislinux/wsl-scripts`.

## Development model

- Development happens on `main` with short-lived PR branches.
- Older branches (`checkpoint/calamares-installer-v1`, `x/reboot`, `dev`) are
  historical and do not reflect the current code.

## Scope

Everything in this repository is part of the equislinux organization. Only
references related to building, installing, and provisioning X live here;
user-facing setup logic lives in the `x-scripts` payload.
