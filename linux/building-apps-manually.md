# General considerations for building apps manually

Create a directory `~/src` or `~/Source`

When you want to build apps manually, it's a good practice to create the source directory in a location that's easy to manage, doesn't interfere with system files, and is within your user space.

Here are the most common and recommended places, along with their reasoning:

Recommendation: This is arguably the most common and recommended approach for general development and building from source.

Reasoning:

- It's located directly in your home directory (~), meaning you always have full read/write permissions without needing sudo for cloning or building (only for sudo make install if the app installs system-wide).

- It keeps all your manually downloaded or cloned source code in one organized place.

- It's a clear indicator of what the directory contains.

Example: If you're building Foliate, you might do:

```bash
mkdir ~/src
cd ~/src
git clone --recurse-submodules https://github.com/johnfactotum/foliate.git
cd foliate
# ... then proceed with build steps

```

# Foliate

- GTK4 epub reader
- install using these instructions: [johnfactotum/foliate](https://github.com/johnfactotum/foliate)

To update the Foliate app that was manually built from its GitHub repository, you'll generally follow these steps:

1.  **Navigate to the Source Directory:** Open your terminal and go to the directory where you initially cloned the Foliate source code. For example:

    ```bash
    cd foliate
    ```

    (Replace `foliate` with the actual directory name if it's different.)

2.  **Pull Latest Changes:** Use Git to fetch the latest updates from the GitHub repository:

    ```bash
    git pull
    ```

    If you've made any local modifications to the source code and want to incorporate the upstream changes cleanly, you might consider using `git pull --rebase`.

3.  **Rebuild the Application:** After pulling the latest code, you need to rebuild the application using the same build system you used for the initial installation. Based on the Foliate GitHub page, it uses `meson` and `ninja`. So, you would typically run:

    ```bash
    meson setup build
    ninja -C build
    ```

4.  **Reinstall the Application:** Finally, reinstall the newly built version. This will usually overwrite the previous installation.

    ```bash
    sudo ninja -C build install
    ```

**Important Considerations:**

- **Dependencies:** Ensure all necessary build dependencies are up-to-date on your system.
- **Conflicts:** If you've made significant local changes to the source code, `git pull` might result in merge conflicts that you'll need to resolve manually.
- **Alternative Installation Methods:** While you've chosen to build from source, keep in mind that for easier updates, applications like Foliate are often available via package managers (e.g., Flatpak from Flathub) or distribution-specific repositories (like in Fedora). These methods automate the update process.

# Important note about gjs

Even though you have gjs installed (version 1.84.2), the meson build system is specifically looking for the development files for gjs-1.0 via pkg-config or cmake.

The runtime library (the gjs package you have) allows applications to use GJS, but to build applications that depend on GJS, you need the header files and pkg-config metadata. These are typically provided in a separate "development" package.

On Fedora, the package you need to install is gjs-devel.

Please open your terminal and run the following command:
Bash

sudo dnf install gjs-devel

After the installation completes, try running the meson setup build and ninja -C build commands again from your /home/tom/src/foliate directory. This should resolve the "gjs-1.0" dependency error.

You need to install gjs-devel, gtk4-devel, libadwaita-devel, webkitgtk6.0-devel
