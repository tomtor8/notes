# Set default applications

### 1. Identify the MIME Type

Before you can set a default, you need to know the specific "MIME type" of the file. You can find this using the `xdg-mime` tool:

```bash
xdg-mime query filetype filename.pdf
# Output might be: application/pdf

```

### 2. Find the `.desktop` File

Linux uses `.desktop` files to identify applications. You need the exact name of the file located in `/usr/share/applications/` or `~/.local/share/applications/`.

```bash
ls /usr/share/applications | grep -i "zathura"
# Output might be: org.pwmt.zathura.desktop

```

### 3. Set the Default

Once you have the MIME type and the `.desktop` filename, run:

```bash
xdg-mime default org.pwmt.zathura.desktop application/pdf

```

---

### 📂 Where is this saved?

Your changes are written to a plain text file: `~/.config/mimeapps.list`.
As a **Neovim** user, you might find it faster to just edit this file directly. It looks like this:

```ini
[Default Applications]
application/pdf=org.pwmt.zathura.desktop
text/plain=nvim.desktop
image/png=org.gnome.Loupe.desktop

```
