# 💻 Pacman Cheat Sheet

[ditig web page](https://www.ditig.com/pacman-cheat-sheet)

**Pacman**, a portmanteau of "package manager", is the package manager used by **Arch Linux** and its derivatives. It is a powerful tool that allows users to install, update, and manage software packages, whether they are from the official repositories or the user's own builds.

Here are the most essential `pacman` commands:

## ⬆️ Updating

- **Update system**
  ```bash
  pacman -Syu
  ```
- **Update pacman database**
  ```bash
  pacman -Syy
  ```
- **Ignore package updates**
  To ignore updates for a specific package, add the package to the `IgnorePkg` line in `/etc/pacman.conf`.

---

## 📦 Installing packages

### Installing from official repositories

- Install `package_name` package from the official repositories:
  ```bash
  pacman -S package_name
  ```

### Install local package

- Install `package_name` package from a local file:
  ```bash
  pacman -U/path/to/package_name
  ```

### Install from PKGBUILD

- Install `package_name` from local **PKGBUILD** file:
  ```bash
  makepkg -si package_name
  ```

---

## 🗑️ Removing packages

- **Simple remove**
  Remove `package_name` package:
  ```bash
  pacman -R package_name
  ```
- **Remove package incl. dependencies**
  Remove `package_name` package including unneeded dependencies:
  ```bash
  pacman -Rs package_name
  ```
- **Remove package, incl. dependencies and configuration file**
  Remove `package_name` package including unneeded dependencies and global configuration file:
  ```bash
  pacman -Rns package_name
  ```
- **Remove unneeded packages and their dependencies**
  Remove multiple unneeded packages including unused dependencies:
  ```bash
  pacman -Rns $(pacman -Qdtq)
  ```

---

## 🧹 Removing cached packages

Pacman stores all downloaded packages in `/var/cache/pacman/pkg/` and does not automatically delete old or uninstalled packages from the cache.

- **Remove cached versions of installed and uninstalled packages**
  Remove all cached versions of installed and uninstalled packages, except for the most recent three (default):
  ```bash
  paccache -r
  ```
- Remove all cached versions of installed and uninstalled packages, except for the most recent one:
  ```bash
  paccache -rk1
  ```
- **Remove cached versions of uninstalled packages**
  Remove all cached versions of uninstalled packages only:
  ```bash
  paccache -ruk0
  ```
- Remove all the cached packages that are not currently installed including the unused sync database:
  ```bash
  pacman -Sc
  ```
- **Remove all files from the cache** (most aggressive approach):
  ```bash
  pacman -Scc
  ```

---

## 🔎 Search & Query

- Retrieve `package_name` package information:
  ```bash
  pacman -Qi package_name
  ```

---

## 🔙 Rollback

- To rollback to a previous version of a package:
  ```bash
  pacman -U /var/cache/pacman/pkg/package_name-version.pkg.tar.zst
  ```
