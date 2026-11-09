# x-scripts — overview

`x-scripts` is the **provisioning payload and CLI** of the X system. It is the
`equislinux/scripts` repo (formerly the `x` repo); it replaced the
Calamares-based graphical installer with scripted provisioning, with its own
implementation and branding.

## Role in the organization

| Repo  | Role |
|-------|------|
| `equislinux/x` | The distro: archiso profile, ISO/WSL builds and install flow. |
| `equislinux/scripts` | **This repo**: provisioning phases, user setup, `x` CLI, migrations and themes, packaged as `x-scripts`. |
| `equislinux/xpkg` / `equislinux/xpm` | Rust packaging/package-manager tooling. |
| `equislinux/x-repo` | Package repository + portal. |

The distro (`x`) consumes this repo packaged as the `x-scripts` package; the
installer runs the root phases (`x setup`) during install and the user phase
(`x setup --user`) at first boot. The Rust tooling (`xpkg`/`xpm`) builds and
drives the native `.xp` endpoint; the system itself is provisioned with
pacman + `[x]`.

## What this repo provides

- `install/` — phase orchestrators for system (root) and user provisioning,
  with idempotent sync helpers and the generation engines (`xgen.sh` for
  system snapshots, `xgen-home.sh` for dotfile generations).
- `bin/` — the `x` CLI (dispatcher + subcommands by naming convention),
  including `x gen ...` and `x home ...`.
- `hooks/` — the `pacman-gen.sh` wrapper and the alpm hook sources
  (`hooks/alpm/`, installed to `/usr/share/libalpm/hooks/`).
- `skel/`, `etc/`, `config/` — dotfile seeds for `/etc/skel`, `/etc`
  drop-ins and user configs.
- `hardware/`, `tools/` — optional modules (NVIDIA, QEMU/libvirt, node) and
  the Hyprland and niri desktop setup tools. KDE Plasma is installed by the
  text installer, not by this payload.
- `migrations/`, `themes/` — per-user migrations and palette themes.
- `packaging/` — the `x-scripts` PKGBUILD and the offline config snapshot
  generator (`vendor-config.sh`).
- `test/` — local tests without root (`test/smoke.sh`).

## Role in the system

On an installed x system the package installs everything below
`/usr/share/x` and exposes the CLI as `/usr/bin/x` (a symlink to
`/usr/share/x/bin/x`). The payload is what turns a freshly pacstrapped Arch
into a provisioned x machine:

1. Root phases configure the system (skel, `/etc` drop-ins, hardware modules,
   services).
2. The user phase seeds the home, syncs dotfiles and provisions the desktop:
   Hyprland (offline snapshot or online via `equisdots/dots`) or niri (online
   via `equisdots/niri-meta`). KDE Plasma is handled by the text installer.
3. `x` is the day-to-day CLI for themes, migrations and updates, plus
   **generations**: `x gen` versions the system (bootable snapshots, rollback,
   granular restore) and `x home` versions the user's dotfiles.

See `cli.md`, `provisioning.md`, `generations.md`, `hyprland.md`, `niri.md`
and `packaging.md`.

## Configuration sources

The desktop configs are **not** maintained in this organization: the equisdots
org owns the stacks (`equisdots/hyprland`, `equisdots/niri`,
`equisdots/niri-shell`, `equisdots/niri-login`, `equisdots/niri-meta`, plus
the shared `shell`, `palettes`, `theme-sync`, `davincix`, `timex` and `login`
repos; branch `main`). App configs come from `xscriptor-colors/terminal`
(kitty/starship) and `xscriptor-colors/nvim`. All are used read-only; the
official installers are `equisdots/dots` (Hyprland) and `equisdots/niri-meta`
(niri). Only the Hyprland stack is vendored for offline use; niri is installed
online. KDE Plasma branding lives in the `x-kde-settings` package in `[x]`.
See `hyprland.md`, `niri.md` and ADR-0005.

## Status

This repo's deliverables are tracked on **`main`**. The payload is published
as `x-scripts` 0.1.0-48, is bundled in the 2026.11.04 ISO, and the public `[x]`
repository serves the same 0.1.0-48. The package also ships the non-interactive
`niri-install.sh` tool used by the text installer. Remaining audit items:
`x-base.packages` without a consumer, CLI polish and more coverage for the
helpers/phases.

WSL lives in its dedicated repos: equislinux/wsl and equislinux/wsl-scripts.
