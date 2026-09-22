# 📝 Manual Snapper Snapshot Configuration

`Snapper` creates automatic `home` and `root` backups.


The goal was to disable automatic snapshot creation while maintaining robust, automatic cleanup of non-essential snapshots.

### 1\. Disabling Automatic Creation

The core step was stopping the timer responsible for creating time-based snapshots.

  * **Action:** Stop and disable the timeline timer.
    ```bash
    # check the state
    sudo systemctl status snapper-timeline.timer
    # stop and disable
    sudo systemctl stop snapper-timeline.timer
    sudo systemctl disable snapper-timeline.timer
    ```

## 2\. Configuration File Setup

The configuration files (`/etc/snapper/configs/root` and `/etc/snapper/configs/home`) were edited for clarity and to disable irrelevant algorithms.

See the copies of the config files in my dotfiles.

| Setting | Value | Rationale |
| :--- | :--- | :--- |
| `TIMELINE_CREATE` | `"no"` | Stops the creation logic (redundant but clean). |
| `TIMELINE_CLEANUP` | `"no"` | Skips the time-based deletion algorithm. |
| `NUMBER_CLEANUP` | `"yes"` | **Kept ENABLED** to automatically delete the oldest non-pinned manual snapshots when the `NUMBER_LIMIT` is reached. |
| `EMPTY_PRE_POST_CLEANUP` | `"yes"` (Root) / `"no"` (Home) | **Kept ENABLED for Root** to delete useless pre/post pairs after system updates. Disabled for Home. |
| `ALLOW_USERS` (Home) | `<your-username>` | Allows you to manage `/home` snapshots without `sudo`. |

## 3\. Cleanup Timer (Essential)

The cleanup timer was kept **enabled** to enforce your `NUMBER_LIMIT` and disk space limits (`SPACE_LIMIT`/`FREE_LIMIT`).

  * **Action:** Ensure the cleanup timer is active.
    ```bash
    sudo systemctl status snapper-cleanup.timer
    sudo systemctl enable --now snapper-cleanup.timer
    ```

## 4\. Manual Snapshot Commands

Snapshots are now only created when you explicitly command them.

| Action | Root Command | Home Command |
| :--- | :--- | :--- |
| **Create** | `sudo snapper create --description "Manual Snap"` | `snapper -c home create --description "Manual Home Snap"` |
| **List** | `sudo snapper list` | `snapper -c home list` |
| **Delete** | `sudo snapper delete <ID>` | `snapper -c home delete <ID>` |

You can also explicitly target the `root` config using `sudo snapper -c root create --description "Some snap".
Without the `-c` flag the command defaults to `root` config.

## 5\. Snapshot Protection (Pinning)

To protect a snapshot from all automatic cleanup routines, you modify its cleanup algorithm.

  * **Pin (Protect):**
    ```bash
    sudo snapper modify --cleanup-algorithm "" <snap ID>
    ```
  * **Unpin (Re-expose to cleanup):**
    ```bash
    sudo snapper modify --cleanup-algorithm "timeline" <snap ID>
    ```

## 6\. GRUB Integration

After creating, modifying, or deleting any **root** snapshot, you must update the GRUB boot menu configuration.

You must have the `grub-btrfs` installed (it is preinstalled if your bootloader is GRUB.

  * **Action:**
    ```bash
    sudo grub-mkconfig -o /boot/grub/grub.cfg
    ```
    *(Note: This is **not** required for `/home` snapshots).*

## 7\. Modifying the snap description

```bash
sudo snapper -c home modify --description "Better Description" <snap ID>
```

## 8\. 🗑️ Deleting Snapshots

Deletion removes the snapshot entry and eventually frees the disk space (after Btrfs removes unreferenced blocks).

- **Delete Root Snap:**

```bash
sudo snapper delete <ID>
```

- **Delete Home Snap:**

```bash
snapper -c home delete <ID>
```

## 9\. ↩️ Rollback (System Recovery)

Rollback is the final step, used only when a system update or change has broken your root filesystem.

### A) Rollback Preparation (Root Only)

1. Reboot the system.
2. In the GRUB menu, select the "Bootable snapshots" option.
3. Choose the known-good snapshot (e.g., ID 65) that you wish to roll back to. The system will boot into a read-only version of that state.

### B) Executing the Rollback

1. Once booted into the read-only snapshot, open a terminal.
2. Execute the rollback command:

```bash
sudo snapper rollback <ID>
```

(e.g., `sudo snapper rollback 65`)

3. Snapper's Action: This command sets the default Btrfs subvolume to a read/write copy of the selected snapshot.
4. Reboot:

```bash
sudo reboot
```

Your system will now boot into the restored, read/write snapshot as the new system root.
