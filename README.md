# Arch Linux Rootfs

Rootfs tarballs of [Arch Linux](https://archlinux.org) based on the [bootstrap tarballs](https://archive.archlinux.org/iso) with `mv root.x86_64/* .`.

You may need to run the following commands on a container created by importing the tarball.

```sh
# Set up a machine ID for systemd
rm /etc/machine-id
systemd-machine-id-setup

# Initialize a pacman keyring and load the ArchLinux's keys
pacman-key --init
pacman-key --populate archlinux

# Add a pacman repository mirror to the list
echo "Server = https://geo.mirror.pkgbuild.com/\$repo/os/\$arch" >> /etc/pacman.d/mirrorlist
```

## `make-rootfs`

You can use [`make-rootfs`](./make-rootfs) to get a rootfs of the latest Arch Linux.

```sh
make-rootfs
```

Then a tarball is in the current directory.
