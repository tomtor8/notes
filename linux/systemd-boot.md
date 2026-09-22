# Safeguard the `boot` directory

- change the `/boot` entry in the `/etc/fstab` file; i.e. modify the fmask and dmask like this:

```bash
UUID=5B81-3FFA      	/boot     	vfat      	rw,relatime,fmask=0077,dmask=0077,codepage=437,iocharset=ascii,shortname=mixed,utf8,errors=remount-ro	0 2
```

- after this you'll need to use the root user to access this directory (by `su` command)

# Automatic systemd-boot updates

Enable the following service:

`sudo systemctl enable systemd-boot-update.service`

# Configuration

`/boot/loader/loader.conf` => see `man loader.conf` for detailed manual

```conf
default arch.conf
timeout 3
console-mode max
editor no
```

`/boot/loader/entries/arch.conf`

```conf
title   Arch Linux
linux   /vmlinuz-linux
initrd  /intel-ucode.img
initrd  /initramfs-linux.img
options root=UUID=9ffc2881-f4b8-4cf1-9fa3-a3e65f7dd088 rootflags=subvol=@ rw
```

# Creating a new backup entry from a snapper snapshot

Here we are using the snapshot 12 (`sudo snapper list`). On emergency boot select this entry and boot in. Then you'll need to `snapper rollback`.

```text
title   Arch Linux (Snapshot 12 - Recovery)
linux   /vmlinuz-linux
initrd  /intel-ucode.img
initrd  /initramfs-linux.img
options root=UUID=9ffc2881-f4b8-4cf1-9fa3-a3e65f7dd088 rootflags=subvol=/@/.snapshots/12/snapshot rw
```

# Resize the boot menu

- make sure you use a **wired** keyboard
- after the boot menu shows up press the `r` key repeatedly and settle for the zoom that suits you
- to change to the default resolution press `R` (shift+r)
- the changed resolution will be automatically saved to efi variables

