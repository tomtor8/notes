# How to apply your custom gtk3 or gtk4 theme to a flatpak app

- you need to grant permission to `$HOME/.config/gtk-4.0` or `$HOME/.config/gtk-3.0` directory
- the above mentioned directories containg my custom `gtk.css` files - see my dotfiles

- `flatpak list` - view you installed flatpak apps with their respective IDs
- then run this command, in this case for the `Parabolic` app

```bash
sudo flatpak override --filesystem=xdg-config/gtk-4.0:ro org.nickvision.tubeconverter
```

this command creates a file `/var/lib/flatpak/overrides/org.nickvision.tubeconverter`

**Contents of the file**

```text
[Context]
filesystems=xdg-config/gtk-4.0:ro;
```

After deleting or reinstalling the Parabolic app, don't forget to remove this file from the `/var/lib/flatpak/overrides` directory if you don't want to use the custom css.
