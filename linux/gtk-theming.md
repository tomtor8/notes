# GTK Theming

## Installation

[adw-gtk3 on GitHub](https://github.com/lassekongo83/adw-gtk3?tab=readme-ov-file)

On Arch install the package:

```bash
sudo pacman -S adw-gtk-theme
```

Follow the information on setting the dark theme.  
I'm not sure if the following command are really necessary - needs further testing.

**Dark theme:**

```bash
gsettings set org.gnome.desktop.interface gtk-theme 'adw-gtk3-dark' && gsettings set org.gnome.desktop.interface color-scheme 'prefer-dark'
```

**Light theme:**

```bash
gsettings set org.gnome.desktop.interface gtk-theme 'adw-gtk3' && gsettings set org.gnome.desktop.interface color-scheme 'default'
```

**Revert to default:**

```bash
gsettings set org.gnome.desktop.interface gtk-theme 'Adwaita' && gsettings set org.gnome.desktop.interface color-scheme 'default'
```

## GTK config directories

Place the gtk.css file to `~/.config/gtk-3.0/` and `~/.config/gtk-4.0/` directories.  
The gtk.css files with different names e.g., `catppuccin-mocha-lavender.css` are placed in my gtk dotfiles.  
Don't forget to rename the files when you place them in the gtk-3.0 or 4.0 directories.

## Examples of css files

[lassekongo83/adw-colors](https://github.com/lassekongo83/adw-colors)
[catppuccin/adw](https://github.com/claymorwan/catppuccin/tree/main/adw#adw-catppuccin)

## Setting custom icons

If you are using Noctalia-shell and initiate it using systemd noctalia.service, don't forget to edit the service file and add the environment variable:

```bash
systemctl edit --user noctalia.service
```

Add variables in the [Service] section:
The Screen scale factor is only an example

```
[Service]
Environment="QT_QPA_PLATFORM=wayland;xcb"
Environment="QT_QPA_PLATFORMTHEME=gtk3"
Environment="QT_AUTO_SCREEN_SCALE_FACTOR=1"
```

This example is using `papirus-icon-theme`.

In the `.config/gtk-3.0/settings.ini`

```ini
[Settings]
gtk-icon-theme-name=Papirus-Dark
```

For GTK 4 (Libadwaita): GTK 4 apps often ignore the config files. You need to set the value via gsettings:


```bash
gsettings set org.gnome.desktop.interface icon-theme 'Papirus-Dark'
```
