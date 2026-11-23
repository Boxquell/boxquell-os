# boxquell-os

Operating systems for [Boxquell](https://github.com/Boxquell/boxquell) sandboxes. Boxquell adds this repository by itself on first use.

```sh
boxquell os ls                    # what there is, and what is on this computer
boxquell new mybox --os ubuntu    # downloads the system on first use
boxquell os build ubuntu          # or build it yourself (needs Docker)
```

The [releases](https://github.com/Boxquell/boxquell-os/releases) of this repository hold ready-made disk images of every system, for x86_64 and ARM64 (Arch Linux has no official ARM64 image, so it is x86_64 only), built by the `Images` workflow whenever a definition changes. Boxquell downloads from the latest one and checks the file against its `SHA256SUMS`.

| Id | System | Recipes that fit | Backends |
|---|---|---|---|
| `debian` | Debian 13 (default) | all official recipes | krun, qemu |
| `debian-12` | Debian 12 | all official recipes | krun, qemu |
| `ubuntu` | Ubuntu 24.04 | agents, Node.js, Python, Go, Rust and most tools | krun |
| `ubuntu-22-04` | Ubuntu 22.04 | agents, Node.js, Python, Go, Rust and most tools | krun |
| `ubuntu-26-04` | Ubuntu 26.04 LTS | agents, Node.js, Python, Go, Rust and most tools | krun |
| `alpine` | Alpine 3 | none yet | krun |
| `arch` | Arch Linux (x86_64 only) | none yet | krun |
| `alma` | AlmaLinux 9 | none yet | krun |
| `alma-10` | AlmaLinux 10 | none yet | krun |
| `alma-8` | AlmaLinux 8 | none yet | krun |

Every version of a system is its own definition with its own id. Definitions that share a `group` are shown as one system with a version chooser.

## How a definition works

One TOML file per system in `os/`. The file name is its id (lowercase letters, digits and `-`). The file without a version suffix is the default of its group; the other versions follow by descending `version`.

```toml
name = "Ubuntu 24.04"
group = "ubuntu"                  # definitions with the same group are versions of one system
version = "24.04"                 # the label in the version chooser
description = "Ubuntu LTS."
icon = "ubuntu.svg"               # optional, a single-colour SVG in the icons/ directory
family = "ubuntu"                 # recipes say which families they support; default is debian
package_hosts = ["archive.ubuntu.com", "security.ubuntu.com"]   # what recipes get for `@packages`
image = "ubuntu:24.04"            # the container image the system is built from
setup = '''
# Runs as root while the image is built. Install what every sandbox needs:
# bash, sudo, an OpenSSH server, curl, git, tar, iproute2, e2fsprogs (resize2fs),
# util-linux (runuser) and CA certificates.
apt-get update && apt-get install -y --no-install-recommends ...
'''
```

Boxquell then adds the user `dev` with passwordless sudo, the ssh settings and its own start scripts, and packs the result into a disk image. A system that installs a kernel and an initrd under `/boot` (as Debian does here) also runs on the qemu backend; the others use the kernel libkrun brings.

Anyone can publish more systems: put an `os/` directory into a repository and add it with `boxquell repo add <url>`. A definition is a script that runs with full rights inside the build container and decides what every sandbox of that system contains, so add only repositories you trust.

Icons in `icons/` come from [Simple Icons](https://simpleicons.org) (CC0). The logos remain trademarks of their owners.

## License

Licensed under either of [Apache License 2.0](LICENSE-APACHE) or [MIT license](LICENSE-MIT) at your option.
