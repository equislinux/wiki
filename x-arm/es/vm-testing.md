# Pruebas en VM en Apple Silicon

> Otros idiomas: [English](../en/vm-testing.md)

Cómo ejecutar el ISO aarch64 de X — o un sistema X ya instalado — en una
MacBook.

## Requisitos

- Un Mac con Apple Silicon (arm64).
- **UTM** (`brew install --cask utm`) o QEMU directo
  (`brew install qemu`).
- El ISO: `~/Downloads/x-<version>-aarch64.iso` (o la descarga publicada en
  SourceForge bajo `equislinux/arm/iso`).

## Opción A — UTM (recomendada)

1. UTM → **+** → *Create a New Virtual Machine*.
2. **Virtualize** (Apple Virtualization, rápido). Si no aparece GRUB, repite
   con **Emulate** (backend QEMU).
3. Sistema: **Linux**, *Boot ISO Image* → selecciona el ISO.
4. RAM 4096 MB, 4 CPUs, disco nuevo de 40 GB (o *Import Drive* con un
   `.qcow2` existente, interfaz VirtIO).
5. Guarda y dale a **Play**. GRUB muestra `x Linux install medium (aarch64,
   UEFI)` → Enter → el instalador de texto te guía.
6. Al terminar (`installation complete`): apaga, expulsa el CD (Drives →
   icono de expulsar), arranca de nuevo → GRUB desde el disco → X Linux
   arranca.

## Opción B — QEMU directo

```bash
cd ~/Documents/repos/equislinux/x-arm
./scripts/vm.sh --gui --iso ~/Downloads/x-2026.10.30-aarch64.iso \
    --disk ~/X-Linux-VM/x-aarch64.qcow2
```

`--gui` abre una ventana Cocoa (virtio-gpu). Sin él, la sesión corre por la
consola serie (añade `console=ttyAMA0` a los parámetros del kernel para ver
salida por serie).

## Consejos para el instalador

- Kernel: elige **`linux-aarch64`** (estable); el kernel rc entra en conflicto
  con el de la lista de paquetes.
- Escritorio: `hyprland` instala el stack equisdots (render por software —
  llvmpipe); `none` es la opción ligera. **No hay aceleración 3D** en VMs:
  desactiva blur/animaciones.
- Pila de virtualización: di que **no** — las VMs anidadas no aportan aquí.
- Gestor de arranque: **GRUB** (`arm64-efi`); systemd-boot no está probado en
  ARM64.

## Límites conocidos

- Sin aceleración 3D ni decodificación de vídeo (llvmpipe).
- `x gen new` es best-effort salvo que se instale `x-scripts-arm`
  (ver el repositorio `scripts-arm`).
- Las bases de datos de ALARM no están firmadas; pacman necesita
  `SigLevel = Required DatabaseOptional` (ya incluido).
