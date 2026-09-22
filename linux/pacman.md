# Pacman and yay

## Ignoring/Pinning Updates

We discussed how to exclude packages from the update process (`pacman -Syu` or `yay -Syu`).

- **Permanent Exclusion:** Edit the `/etc/pacman.conf` file and add package names (space-separated) to the `IgnorePkg` directive.
  ```conf
  [options]
  IgnorePkg = package1 package2
  ```
- **Temporary Exclusion:** Use the `--ignore` flag during the update command (comma-separated, no spaces).
  ```sh
  yay -Syu --ignore=package3,package4
  ```
- ⚠️ **Warning:** Using these methods creates a **partial upgrade**, which can introduce instability on rolling release systems like Arch/Cachyos.

---

## Listing Only Outdated Packages

The safest way to check for updates without initiating an upgrade or partial sync is using tools from `pacman-contrib`.

- **Official Repo Packages:** Use the `checkupdates` script (part of `pacman-contrib`).
  ```sh
  checkupdates
  ```
- **AUR & Official Packages:** Run the full `yay` update command and choose **N** (No) when prompted to install.
  ```sh
  yay -Syu
  ```
- **Only AUR Packages:** Query for outdated AUR packages.
  ```sh
  yay -Qua
  ```

---

## Pacman-Contrib Utilities

The `pacman-contrib` package provides essential maintenance scripts:

| Utility      | Function                                              | Common Command Example                                    |
| :----------- | :---------------------------------------------------- | :-------------------------------------------------------- |
| checkupdates | Safely lists available updates.                       | checkupdates                                              |
| paccache     | Cleans the package cache (/var/cache/pacman/pkg).     | sudo paccache -r (Keeps 3 most recent installed packages) |
| pacdiff      | Helps merge .pacnew and .pacsave configuration files. | sudo pacdiff                                              |
| pactree      | Shows package dependency tree.                        | pactree firefox                                           |

---

## Essential Pacman & yay Flags

> **Note:** `yay` commands are basically the same but do not use the `sudo`

| Operation                | Pacman Command (Official Repos) | Purpose                                                                                                          |
| :----------------------- | :------------------------------ | :--------------------------------------------------------------------------------------------------------------- |
| Full System Update       | sudo pacman -Syu                | Updates package databases, then upgrades all installed packages. yay includes AUR packages.                      |
| Install Package          | sudo pacman -S ...              | Installs a package and its dependencies. yay searches/installs from both repos and AUR.                          |
| Remove Package           | sudo pacman -R ...              | Removes the package only.                                                                                        |
| Remove w/ Deps & Configs | sudo pacman -Rns ...            | The safest way to remove a package, its dependencies (that no other package needs), and its configuration files. |
| Search Repos/AUR         | pacman -Ss ...                  | Searches package names and descriptions in official repos. yay includes AUR results.                             |
| Query Installed          | pacman -Qs ...                  | Searches names of installed packages.                                                                            |
| List Files               | pacman -Ql ...                  | Lists all files owned by an installed package.                                                                   |
| View Package Info        | pacman -Si ...                  | Shows detailed information (dependencies, maintainer, size) for a package in the sync database.                  |
| View Installed Info      | pacman -Qi ...                  | Shows detailed information for an installed package.                                                             |

---

## Advanced Maintenance & Query Flags

These flags are often used in combination or are essential for system hygiene.

`-Qdt` (pacman): queries installed packages (-Q), filtering for those installed as Dependencies (-d) that are now Total orphans (-t), meaning nothing requires them. Useful for finding unused packages.

`-Qe` (pacman): queries packages that were _explicitly installed_ by the user (not as dependencies).

`-Qm` (pacman): queries _maintained_ (installed) packages that are foreign (not in sync databases). This is the key way to **see all your AUR packages**. |

`checkupdates` (pacman-contrib): safely checks and lists packages that are outdated without touching the main database.

`-kX` (paccache): keeps only the last X versions of packages in the cache (/var/cache/pacman/pkg). Use `sudo paccache -rk3` to keep 3.

`-a` (yay): restricts the operation to only the AUR. Example: `yay -Sua` updates only your AUR packages.

`--clean` (yay): removes leftover build files and packages after an AUR installation, helping to keep your cache clean.

`--needed` (pacman) `-S ...` : Prevents Pacman from reinstalling packages that are already up-to-date. Useful when installing groups of packages.

`--noconfirm` (pacman/yay): bypasses all confirmation prompts. Use with extreme caution, especially with yay on AUR packages, as you skip the PKGBUILD review.

---

## Cleaning Unused Packages

To clean up packages that were installed as dependencies but are no longer needed by any explicitly installed package (often called **orphans**), you can combine the `-Qdt` query with the `-Rns` removal:

```bash
pacman -Qdtq | sudo pacman -Rns -
```

The `-q` flag makes the output quiet (just the package names), which is piped into the removal command. The `-` tells `pacman` to read the list of packages from the pipe. This is a common practice for system maintenance on Arch.

## List every file owned by an installed package

```bash
pacman -Ql <package-name>
```

- use `less` if the output is too long

## Pacmannew files

- after every upgrade run this command `DIFFPROG="nvim -d" sudo -E pacdiff`
- the `pacdiff` is installed via `pacman-contrib`
- first view the changes and then merge, modify or remove them as needed
