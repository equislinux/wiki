# WSL (X Linux for Windows Subsystem for Linux)

Two dedicated repositories provide X Linux inside WSL (terminal-only, no
graphical environment):

- `equislinux/wsl` — distro side: builds the importable minimal rootfs, ships
  `wsl.conf`/`.wslconfig` templates and a Windows PowerShell importer
  (`install.ps1`).
- `equislinux/wsl-scripts` — setup side: friendly two-step installer
  (system stage as root, user stage) that sets language, keyboard, timezone,
  user with sudo and the environment variables WSL needs.

Flow: build the rootfs on an Arch host, import it on Windows with
`install.ps1`, then run the `wsl-scripts` installer twice (root, then user).

## Reading

- English: `en/wsl/`, `en/wsl-scripts/`
- Español: `es/wsl/`, `es/wsl-scripts/`
