# Source: https://docs.rewire.run/getting-started/installation

Getting Started

# Installation

Install Rewire on macOS or Linux.

Pick the installation method that best fits your setup. The package always includes both `rewire` (the bridge) and `rewire-viewer` (the custom Rerun-based viewer).

Install scriptpixi (prefix.dev)APT (Debian/Ubuntu)Nix

The fastest way to install Rewire — detects your OS and architecture, downloads the latest binaries, and installs them to `~/.local/bin`.

```
curl -fsSL https://rewire.run/install.sh | sh
```

That target is a directory you own, so later upgrades never need `sudo`. The script tells you which line to add to your shell profile when `~/.local/bin` is not on your `PATH`. Override the location with `INSTALL_DIR`, keeping in mind that a root-owned directory makes every upgrade require `sudo`.

```
curl -fsSL https://rewire.run/install.sh | INSTALL_DIR=/usr/local/bin sh
```

The script installs the latest release unless you name one. Set `REWIRE_VERSION` to pin a specific version, which is the way back to a known-good build if a release regresses on your setup.

```
curl -fsSL https://rewire.run/install.sh | REWIRE_VERSION=0.9.1 sh
```

When an older copy of `rewire` comes first on your `PATH`, the script warns that it shadows the new install and prints how to clear it. Re-running with `REWIRE_REMOVE_SHADOWED=1` removes that copy for you.

If you use [pixi](https://prefix.dev), install Rewire from our channel:

```
pixi global install -c https://prefix.dev/rewire rewire
```

Add the Rewire APT repository and install via `apt`:

```
curl -fsSL https://apt.rewire.run/key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/rewire.gpg
echo "deb [signed-by=/usr/share/keyrings/rewire.gpg] https://apt.rewire.run stable main" | sudo tee /etc/apt/sources.list.d/rewire.list
sudo apt update && sudo apt install rewire
```

Run Rewire without installing anything:

```
nix run github:rewire-run/rewire
```

Add it to a NixOS or nix-darwin configuration by taking the flake as an input:

```
{
  inputs.rewire.url = "github:rewire-run/rewire";

  # then, in a module
  environment.systemPackages = [ rewire.packages.x86_64-linux.default ];
}
```

The flake also exposes `overlays.default`, which puts `rewire` on `pkgs`. Rewire is unfree, so the overlay route needs `nixpkgs.config.allowUnfree = true` in your own configuration — the `packages` output already allows it, so `nix run` and `nix profile` need nothing.

For an imperative install, `nix profile add github:rewire-run/rewire`.

The flake repacks the published release archives rather than building from source, so it installs the same binaries every other method does. `aarch64-linux`, `x86_64-linux` and `aarch64-darwin` are covered.

## [Supported platforms](https://docs.rewire.run/getting-started/installation#supported-platforms)

| OS | Architecture |
| --- | --- |
| macOS | aarch64 (Apple Silicon) |
| Linux | x86\_64, aarch64 |

Intel macOS is not supported. No build is published for it, and Rosetta cannot run an Apple Silicon binary on an Intel machine. Building from source is the only option there.

## [Verify installation](https://docs.rewire.run/getting-started/installation#verify-installation)

```
rewire --version
```

## [Updating](https://docs.rewire.run/getting-started/installation#updating)

Install scriptpixiAPT

```
rewire upgrade
```

Both `rewire` and `rewire-viewer` are updated, and neither needs `sudo` when they live in a directory you own.

If an earlier version installed `rewire` into `/usr/local/bin`, run the relocation once — without `sudo` — to move both binaries into `~/.local/bin`. An install placed there cannot move itself, so it keeps asking for `sudo` on every upgrade until you do.

```
rewire upgrade --relocate
```

```
pixi global upgrade -c https://prefix.dev/rewire rewire
```

```
sudo apt update && sudo apt upgrade rewire
```

Move the flake input to the latest release, then rebuild:

```
nix flake update rewire
```

For an imperative install, `nix profile upgrade rewire`.

Rewire checks for new versions in the background and notifies you when an update is available. Upgrading in place only works for installs made by the install script, and it replaces both the bridge and the viewer, each against its own latest release.

An install that predates `~/.local/bin` sits in a root-owned directory, so upgrading it asks for `sudo` every time. Remove the old copies and reinstall once to be rid of that.

```
sudo rm -f /usr/local/bin/rewire /usr/local/bin/rewire-viewer
curl -fsSL https://rewire.run/install.sh | sh
```

## [Uninstalling](https://docs.rewire.run/getting-started/installation#uninstalling)

Install scriptpixiAPT

```
rewire uninstall
```

Both binaries go, along with the version cache kept beside them. Your config file and logs stay where they are, so reinstalling later lands on the settings you had. Add `--purge` to remove those too, and `-y` to skip the confirmation prompt — see [`rewire uninstall`](https://docs.rewire.run/cli-reference#rewire-uninstall).

```
pixi global uninstall rewire
```

```
sudo apt remove rewire
```

Drop it from `environment.systemPackages` and rebuild. For an imperative install:

```
nix profile remove rewire
```

`rewire uninstall` will not touch a Nix install. It recognises the store the same way it recognises pixi, APT and Homebrew, and refuses with the command above rather than deleting read-only paths out from under Nix.

`rewire uninstall` only removes an install the script made. If a package manager owns it, the command refuses and names the one to run instead, so nothing deletes a file out from under `dpkg` or `brew`.

[Introduction\\ \\ Rewire is a drop-in ROS 2 bridge for Rerun. Stream live topics to the viewer in seconds.](https://docs.rewire.run/) [Quick Start\\ \\ Stream your first ROS 2 topics to Rerun in under a minute.](https://docs.rewire.run/getting-started/quick-start)

### On this page

[Supported platforms](https://docs.rewire.run/getting-started/installation#supported-platforms) [Verify installation](https://docs.rewire.run/getting-started/installation#verify-installation) [Updating](https://docs.rewire.run/getting-started/installation#updating) [Uninstalling](https://docs.rewire.run/getting-started/installation#uninstalling)

Last updated on August 26, 2026

© 2026 Rewire