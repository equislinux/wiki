# VM testing on Apple Silicon

> Other languages: [Español](../es/vm-testing.md)

How to run the aarch64 X ISO — or an installed X system — on a MacBook.

## Requirements

- An Apple Silicon Mac (arm64).
- **UTM** (`brew install --cask utm`) or plain QEMU
  (`brew install qemu`).
- The ISO: `~/Downloads/x-<version>-aarch64.iso` (or the published
  SourceForge download under `equislinux/arm/iso`).

## Option A — UTM (recommended)

1. UTM → **+** → *Create a New Virtual Machine*.
2. **Virtualize** (Apple Virtualization, fast). If GRUB does not appear,
   repeat with **Emulate** (QEMU backend).
3. System: **Linux**, *Boot ISO Image* → select the ISO.
4. RAM 4096 MB, 4 CPUs, a new 40 GB disk (or *Import Drive* with an existing
   `.qcow2`, interface VirtIO).
5. Save and **Play**. GRUB shows `x Linux install medium (aarch64, UEFI)` →
   Enter → the text installer guides you.
6. After `installation complete`: shut down, eject the CD (Drives → eject
   icon), start again → GRUB from the disk → X Linux boots.

## Option B — QEMU directly

```bash
cd ~/Documents/repos/equislinux/x-arm
./scripts/vm.sh --gui --iso ~/Downloads/x-2026.10.30-aarch64.iso \
    --disk ~/X-Linux-VM/x-aarch64.qcow2
```

`--gui` opens a Cocoa window (virtio-gpu). Without it, the session runs on
the serial console (add `console=ttyAMA0` to the kernel params for serial
output).

## Installer tips

- Kernel: choose **`linux-aarch64`** (stable); the rc kernel conflicts with
  the one in the package list.
- Desktop: `hyprland` installs the equisdots stack (software rendering —
  llvmpipe); `none` is the lean option. There is **no 3D acceleration** in
  VMs: disable blur/animations.
- Virtualization stack: say **no** — nested VMs are not useful here.
- Bootloader: **GRUB** (`arm64-efi`); systemd-boot is untested on ARM64.

## Known limits

- No 3D/video decode acceleration (llvmpipe).
- `x gen new` is best-effort unless `x-scripts-arm` is installed
  (see the `scripts-arm` repository).
- ALARM databases are unsigned; pacman needs
  `SigLevel = Required DatabaseOptional` (already shipped).
