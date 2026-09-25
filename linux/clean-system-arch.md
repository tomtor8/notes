# System Maintanance and Cleaning for Arch Linux

## Clear Trash

The "Trash" or "Recycle Bin" is where deleted files go before being permanently removed.

**Command Line Method:**

- To empty the current user's trash:

  ```bash
  rm -rf ~/.local/share/Trash/*
  ```

- or manually remove the contens of the directories in Trash

## Clear User Cache

Many applications store cached data in your user's home directory.

**Command Line Method:**

- This is often found in `~/.cache`. While you can delete everything in there, it's safer to delete contents of specific subdirectories, or at least be aware that some applications might rebuild their cache upon next launch, which can take time.

## Pacman cleanup

Pacman cleanup generally involves two main areas:

1.  **Cleaning the Package Cache:** Pacman keeps a cache of all downloaded `.pkg.tar.zst` (or `.pkg.tar.xz`) files in `/var/cache/pacman/pkg/`. Over time, this directory can grow quite large.
2.  **Removing Orphaned Packages:** These are packages that were installed as dependencies of another package, but the original package has since been removed, leaving the dependency unused.

Here are the common commands and methods for cleaning up Pacman:

## Cleaning the Package Cache

You have a few options here, depending on how aggressive you want to be:

- **`sudo pacman -Sc` (Recommended for most users):**
  This command removes all cached packages that are no longer installed on your system. It also removes old versions of installed packages, keeping only the current and previous versions (by default, if `paccache` is used, see below). This is a good balance between freeing space and still being able to downgrade or reinstall a package from the cache if needed.

  ```bash
  sudo pacman -Sc
  ```

- **`sudo pacman -Scc` (Use with caution\!):**
  This command _completely_ clears the package cache, removing all cached packages, regardless of whether they are installed or not. While it frees up the most disk space, it also means you won't be able to downgrade or reinstall any package from your local cache without redownloading it from the repositories. It's generally not recommended unless you are very low on disk space and understand the implications.

  ```bash
  sudo pacman -Scc
  ```

- **Using `paccache` (Recommended, more granular control):**
  `paccache` is a script provided by the `pacman-contrib` package. It offers more flexible ways to manage your package cache. By default, it keeps the last three versions of each installed package and removes older ones.

  First, ensure you have `pacman-contrib` installed:

  ```bash
  sudo pacman -S pacman-contrib
  ```

  Then, you can use `paccache` in various ways:
  - **Keep the last three versions of all installed packages (default):**
    ```bash
    sudo paccache -r
    ```
  - **Keep the last two versions of all installed packages:**
    ```bash
    sudo paccache -rk2
    ```
  - **Remove all cached versions of uninstalled packages:**
    ```bash
    sudo paccache -ruk0
    ```
  - **Perform a dry run (show what would be removed without actually removing it):**
    ```bash
    sudo paccache -dr
    ```

  Many users also set up a systemd timer to run `paccache -r` periodically (e.g., weekly) to automate cache cleanup.

## Removing Orphaned Packages

Orphaned packages are dependencies that are no longer required by any explicitly installed package.

- **List orphaned packages:**

  ```bash
  pacman -Qdt
  ```

  This command queries (`-Q`) packages that are installed as dependencies (`-d`) and are no longer required by any other package (`-t`). The output will be a list of package names.

- **Remove orphaned packages:**
  Once you've reviewed the list from `pacman -Qdt` and are sure you want to remove them, you can pipe the output to `pacman -Rns`:

  ```bash
  sudo pacman -Rns $(pacman -Qdtq)
  ```

  Let's break this down:
  - `pacman -Qdtq`: Lists orphaned packages, but in a quiet format (`-q`) suitable for piping (only package names).
  - `pacman -Rns`:
    - `-R`: Remove packages.
    - `-n`: Do not save configuration files (unless another package explicitly owns them).
    - `-s`: Remove dependencies that are no longer required by any other installed package (recursive removal).
  - `$()`: Command substitution, which runs `pacman -Qdtq` and uses its output as arguments for `pacman -Rns`.

  **Important:** Always review the list of packages `pacman` intends to remove before confirming with `y`. Occasionally, a package might be listed as an orphan that you still want to keep (e.g., if you use a program that relies on it but doesn't explicitly declare it as a dependency in its `PKGBUILD`).

## Keep an ophraned package by making it explicit

If you want to keep a package that's listed as an orphan, you can mark it as explicitly installed:

```bash
sudo pacman -D --asexplicit <package_name>
```

## Analyze disk usage

```bash
du -sh /*
```

## Manage system logs

```bash
sudo journalctl --vacuum-size=500M
# or
sudo journalctl --vacuum-time=7d
```

### Permanent solution

Edit **/etc/systemd/journald.conf** and set **SystemMaxUse=500M**, then restart with `sudo systemctl restart systemd-journald`. This is critical on every server because unbounded journal growth sneaks up at the worst possible time.

## How to Analyze Boot Time with systemd-analyze

Boot time degrades slowly until you suddenly notice your machine takes 30 seconds to reach the desktop. I check it monthly using three commands.

### Check Overall Boot Time

`systemd-analyze` time breaks down the boot into kernel, initrd, and userspace phases.

### Identify the Slowest Services

`systemd-analyze blame` shows which services are taking the longest to start.

Running `systemd-analyze critical-chain` shows the actual dependency chain. This is more useful than blame because it shows which services are on the critical path versus those that start in parallel.

> [!NOTE]
> You can generate a visual SVG timeline of your boot process with `systemd-analyze plot > boot.svg`. Open the resulting file in any web browser to see a detailed Gantt chart of every service, its start time, and its duration.

### How to Manage Systemd Services

Every application that registers a systemd service adds to your boot time. Audit enabled services on every machine. `systemctl list-unit-files --state=enabled --type=service` shows everything that starts at boot. Disable any service not actively used. If the system does not have a printer, run `sudo systemctl disable cups.service`. If Bluetooth peripherals are not needed, disable bluetooth.service.

## How to clean temporary files

On Arch, run:

```bash
sudo find /tmp -type f -atime +7 -delete
```

The find command deletes files in `/tmp` that have not been accessed in more than 7 days. This rarely causes issues because genuinely important temporary files are accessed frequently or stored elsewhere. That said, always check what is in `/tmp` before running this on an unfamiliar machine.
