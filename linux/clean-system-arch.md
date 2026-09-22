# Intro

- see the `clean-system-fedora.md` for general info

"Pacman" refers to the package manager used in Arch Linux and its derivatives, like Manjaro or, in your case, Fedora with a custom Pacman setup (though Fedora typically uses `dnf`). Assuming you're referring to `pacman` as it's used on an Arch-based system (perhaps you meant to say you use Arch instead of Fedora, or you have a specific setup), here's how to clean it up:

Pacman cleanup generally involves two main areas:

1.  **Cleaning the Package Cache:** Pacman keeps a cache of all downloaded `.pkg.tar.zst` (or `.pkg.tar.xz`) files in `/var/cache/pacman/pkg/`. Over time, this directory can grow quite large.
2.  **Removing Orphaned Packages:** These are packages that were installed as dependencies of another package, but the original package has since been removed, leaving the dependency unused.

Here are the common commands and methods for cleaning up Pacman:

### 1 Cleaning the Package Cache

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
  - **Keep only the last one version of all installed packages:**
    ```bash
    sudo paccache -rk 1
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

### 2 Removing Orphaned Packages

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

  **Important:** Always review the list of packages `pacman` intends to remove before confirming with `y`. Occasionally, a package might be listed as an orphan that you still want to keep (e.g., if you use a program that relies on it but doesn't explicitly declare it as a dependency in its `PKGBUILD`). If you want to keep a package that's listed as an orphan, you can mark it as explicitly installed:

  ```bash
  sudo pacman -D --asexplicit <package_name>
  ```

By regularly performing these cleanup steps, especially using `paccache -r` and removing orphaned packages, you can keep your Pacman system tidy and free up disk space.
