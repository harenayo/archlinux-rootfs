# Arch Linux Rootfs

Rootfs tarballs of [Arch Linux](https://archlinux.org) based on the [bootstrap tarballs](https://archive.archlinux.org/iso) with some changes:

- Remove `root.x86_64` directory
- Uncomment the [`geo.mirror.pkgbuild.com`](https://geo.mirror.pkgbuild.com) line of `/etc/pacman.d/mirrorlist`

## `make-rootfs`

You can use [`make-rootfs`](./make-rootfs) to get a rootfs of the latest Arch Linux.

```sh
make-rootfs
```

Then a tarball is in the current directory.
