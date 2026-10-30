# X Linux en aarch64 (x-arm)

> Otros idiomas: [English](../en/index.md)

Este es el índice de documentación del repositorio `equislinux/x-arm`: el
**port de X a aarch64**, construido como un *overlay* sobre el código x86_64
pineado. Los repositorios compañeros son
[`x-arm-repo`](https://github.com/equislinux/x-arm-repo) (el repositorio
pacman `[x]/aarch64`) y
[`scripts-arm`](https://github.com/equislinux/scripts-arm) (el delta ARM del
motor de generaciones).

## Visión general

X Linux en ARM **no** bifurca `equislinux/x`. `x-arm` fija `x` a un commit
(`vendor/x.sha`) y aplica encima un overlay exclusivo de ARM. El árbol
x86_64, las imágenes WSL y todo lo demás quedan intactos por construcción:
un cambio de ARM no puede filtrarse a `main` de `x` por accidente.

El port apunta solo a **aarch64** (UEFI, GRUB `arm64-efi`), basado en
**Arch Linux ARM** (`core`, `extra`, `alarm`, `aur`). Fuera del alcance de
la v1: armv7h, Raspberry Pi, Asahi, Secure Boot y ARM WSL.

Produce:

- un **ISO live solo-UEFI** (`x-<version>-aarch64.iso`), publicado en
  SourceForge bajo `equislinux/arm/iso`;
- el **repositorio `[x]/aarch64`** (firmado), servido desde
  `https://equislinux.github.io/x-arm-repo/repo/aarch64`;
- **imágenes de VM** para Apple Silicon (UTM/Parallels/VMware) y hosts
  `virt` genéricos.

## Estado

| Área | Estado |
|---|---|
| Perfil archiso (UEFI/GRUB, `arm64-efi`, `/boot/Image`) | listo |
| `packages.aarch64` contra Arch Linux ARM | listo |
| Builder Docker (rootfs de Arch Linux ARM) | listo |
| Repositorio `[x]/aarch64` (firmado, GitHub Pages) | listo |
| Instalador (layout UEFI, keyring/mirrors de ALARM, initrd de GRUB) | listo |
| Escritorio (Hyprland + equisdots) | funciona en VMs con render por software (llvmpipe); niri sin probar |
| Generaciones (`x gen`) | vía `x-scripts-arm` (ver `scripts-arm`); si no, best-effort |
| e2e automatizado | no todavía (validado a mano en QEMU/UTM) |
| Secure Boot, Raspberry Pi, Asahi, ARMv7 | fuera de alcance |

## Estructura

```
vendor/x.sha            commit pineado de equislinux/x
vendor/archiso.sha      commit pineado de archlinux/archiso
overlay/                el delta solo-ARM (perfil, paquetes, GRUB, parches)
docker/                 contenedor builder de Arch Linux ARM
scripts/                fetch, overlay, build de repo, build de ISO, VM, publish
docs/                   docs de build (es/en) y criterios de aceptación
```

## Compilación

```bash
docker/build-image.sh                       # una vez: contenedor builder
docker/run.sh scripts/fetch-x.sh            # resolver entradas
docker/run.sh scripts/fetch-archiso.sh
docker/run.sh scripts/apply-overlay.sh      # overlay + verificaciones
docker/run.sh scripts/build-repo.sh         # [x]/aarch64 (--sign para release)
docker/run.sh sudo scripts/build-iso.sh     # ISO live
```

Ver [Pruebas en VM](vm-testing.md) para ejecutar el ISO o una imagen instalada
en Apple Silicon.

## Datos clave

- `archiso` no está empaquetado en ALARM; se usa el árbol upstream pineado
  (soporta `BOOTAA64.EFI`, y `x-arm` parchea la copia del kernel `/boot/Image`
  y la lista de módulos GRUB por plataforma).
- `linux-aarch64` instala `/boot/Image(.gz)` y no trae fichero `pkgbase`; el
  instalador y el delta de generaciones (`scripts-arm`) lo manejan.
- Las bases de datos de ALARM no están firmadas (los paquetes sí):
  `SigLevel = Required DatabaseOptional`.
- La publicación es manual: `scripts/publish-repo.sh` (Pages de x-arm-repo) y
  `scripts/publish-arm.sh` (SourceForge) requieren la clave GPG del proyecto y
  la clave SSH de SourceForge.
