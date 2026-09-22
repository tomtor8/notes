# Configure mounting of partitions and disks

`/etc/fstab`

My custom fstab config on Arch Linux, Nuc with customized external SSD formatted as exfat. The SSD is freely accessible by anyone. If you wish, soft link the SSD to your home directory if you wish.

```text
# Static information about the filesystems.
# See fstab(5) for details.

# <file system> <dir> <type> <options> <dump> <pass>
# /dev/nvme0n1p2
UUID=9ffc2881-f4b8-4cf1-9fa3-a3e65f7dd088	/         	btrfs     	rw,noatime,compress=zstd:3,ssd,discard=async,space_cache=v2,subvol=/@	0 0

# /dev/nvme0n1p2
UUID=9ffc2881-f4b8-4cf1-9fa3-a3e65f7dd088	/home     	btrfs     	rw,noatime,compress=zstd:3,ssd,discard=async,space_cache=v2,subvol=/@home	0 0

# /dev/nvme0n1p2
UUID=9ffc2881-f4b8-4cf1-9fa3-a3e65f7dd088	/var/cache/pacman/pkg	btrfs     	rw,noatime,compress=zstd:3,ssd,discard=async,space_cache=v2,subvol=/@pkg	0 0

# /dev/nvme0n1p2
UUID=9ffc2881-f4b8-4cf1-9fa3-a3e65f7dd088	/var/log  	btrfs     	rw,relatime,compress=zstd:3,ssd,discard=async,space_cache=v2,subvol=/@log	0 0

# /dev/nvme0n1p1
UUID=5B81-3FFA      	/boot     	vfat      	rw,relatime,fmask=0077,dmask=0077,codepage=437,iocharset=ascii,shortname=mixed,utf8,errors=remount-ro	0 2

# /dev/sda1 Samsung external SSD
UUID=F9FF-F8D2	/mnt/sam_ssd     	exfat     	user,rw,umask=000,noatime,noauto,x-systemd.automount,nofail   	0 0
```

Note that some of the partitions have the `noatime` option set for increased performance and less writes to files.  
The `/boot` partition has modified fmask and dmask values so that only the superuser can access it (after using the `su` command).  
The `noauto` option disables automatic mounting at boot, `nofail` is important to prevent the boot from hanging when there is no SSD plugged in, `x-systemd.automount` mounts the disk automatically on first access attempt.

# Unmounting the fstab configured disks

Unmount the disk using the command `sudo umount /mnt/sam_ssd/`.
If you plug in the disk again you can easily mount it again using either `sudo mount -a` or `sudo mount /mnt/sam_ssd`.

# Unmounting the external SSD using Desktop Entry

- this enables to use fuzzel app launcher to quickly unmount the external drive
- create `~/.local/share/applications/unmount-ssd.desktop` then make it executable using `chmod`

```toml
[Desktop Entry]
Type=Application
Name=Unmount External SSD
Comment=Safely unmount the SSD at /mnt/my_ssd
Exec=sh -c 'pkexec umount /mnt/sam_ssd && notify-send "SSD Safe to Remove" "The drive at /mnt/my_ssd has been unmounted." -i drive-removable-media'
Icon=drive-removable-media
Terminal=false
Categories=System;Settings;
```

The `Exec` line executes the unmounting and on succes sends a notification.
If you don't want to see a notification use the following line `Exec=pkexec umount /mnt/sam_ssd`
