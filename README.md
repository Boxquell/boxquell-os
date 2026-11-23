# boxquell-os

Operating systems for [Boxquell](https://github.com/ToBee94/boxquell) sandboxes. Boxquell adds this repository by itself on first use.

```sh
boxquell os ls                    # what can be built, and what is built
boxquell os build ubuntu          # builds the image once (needs Docker and e2fsprogs)
boxquell new mybox --os ubuntu
```

| Id | System | Recipes that fit | Backends |
|---|---|---|---|
| `debian` | Debian 13 (default) | all official recipes | krun, qemu |
| `ubuntu` | Ubuntu 24.04 | agents, Node.js, Python, Go, Rust and most tools | krun |
| `alpine` | Alpine 3 | none yet | krun |

## How a definition works

One TOML file per system in `os/`. The file name is its id.

```toml
name = "Ubuntu 24.04"
description = "Ubuntu LTS."
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

## License

Licensed under either of [Apache License 2.0](LICENSE-APACHE) or [MIT license](LICENSE-MIT) at your option.
