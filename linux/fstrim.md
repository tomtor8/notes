# fstrim SSD optimization

## Introduction

On modern Linux systems, you don't need to manually read data to keep an SSD healthy. Instead, you use the **TRIM** command. TRIM tells the SSD controller which blocks of data are no longer needed so it can wipe them in the background, keeping write speeds fast.

When files are deleted, the filesystem marks their blocks as unused. The storage device underneath may not automatically know those blocks are free unless discard information reaches it. fstrim bridges that gap by telling the lower layer which unused blocks can be discarded. On supported flash storage, this can help with long-term write behavior and wear management. On thin-provisioned virtual disks, it may also help the host reclaim space.

## Verify the eligibility of an SSD

**Storage check:** A non-zero DISC-MAX on your target disk or partition is the sign you want before expecting fstrim to work. A 0B value means the kernel currently sees no discard capability on that path.

The best first check is `lsblk -D`. It prints discard granularity and maximum discard size for each block device. I care most about DISC-MAX: if it is 0B, discard is not exposed for that device path.

### SSD's not eligible for TRIM

**ExFAT external drives can be flashed via Windows**. Windows has built-in optimizations for ExFAT storage layers. Open `Defragment and Optimize Drives`, select your external SSD, and hit `Optimize`. Windows will pass a proper, hard-coded trim cycle directly to your device over the filesystem structure cleanly.

## Confirm the filesystem and mount options with `findmnt`

```bash
findmnt -no SOURCE,FSTYPE,OPTIONS /
```

## Manual TRIM

You can manually run TRIM on all mounted filesystems using:

```bash
sudo fstrim -va
# or
sudo fstrim --all --verbose
```

- `-v`: Verbose (shows you how many bytes were freed).
- `-a`: All compatible mounted filesystems.

You do not pass it a raw disk such as `/dev/nvme0n1`; you pass it a mounted filesystem such as `/`, `/home`, or use `--all` to let it walk eligible mounts.

**Test the command** with `--dry-run`:

```bash
sudo fstrim --dry-run --verbose /
```

> [!Warning]
> If `fstrim` says discard is not supported, do not force anything.  
> Go back to `lsblk -D` and inspect the storage layers.

### When to run `fstrim` manually

1. After deleting a large amount of data, esp. before shrinking, imaging, or cloning a disk.
2. Storage troubleshooting.
3. After changing a storage layer, e.g., moving a filesystem onto a new SSD.

## Automation with `systemd timer`

The best practice on Arch Linux is to enable the Systemd timer. This runs `fstrim` automatically once a week.

```bash
# Enable and start the weekly TRIM timer
sudo systemctl enable --now fstrim.timer
```

You can check its status anytime with:

```bash
systemctl status fstrim.timer
# check the timer configuration
systemctl cat fstrim.timer
```

### Persistent activation of a timer

If `Persistent=true` is present in the configuration, you don't need to worry about missing the scheduled cleanup. When you boot up your computer, `systemd` immediately checks that timestamp log. If it detects that the weekly trigger time passed while the machine was powered down, it bypasses the schedule and triggers `fstrim.service` immediately upon boot.

To see the next scheduled trim:

```bash
systemctl list-timers fstrim.timer
```

### Confirm previous timer runs throught the service journal

```bash
journalctl -u fstrim.service -n 12 --no-pager
```

## Truly "Refreshing" Stale Blocks

If you have an old SSD that suffers from "read speed degradation" on old files (like early Samsung EVO drives), you truly _do_ need to rewrite the data.
The proper way to force the drive to read every single raw sector (including empty space) is using dd directed to the null device:

```fish
# Read the entire raw disk to force internal cell refresh
# replace the nvme0n1 with your device
sudo dd if=/dev/nvme0n1 of=/dev/null bs=1M status=progress
```
