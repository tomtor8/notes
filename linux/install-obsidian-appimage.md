- create a desktop file `$HOME/.local/share/applications/obsidian.desktop` 

```bash
[Desktop Entry]
Name=Obsidian
Exec=/home/tom/AppImages/Obsidian-1.7.7.AppImage --enable-features=UseOzonePlatform --ozone-platform=wayland
Terminal=false
Type=Application
Icon=
Comment=Note App
Categories=Notes;Markdown
MimeType=x-scheme-handler/obsidian;
```

- if you are on Wayland, it is important to have the flags enabled - `--enable-features=UseOzonePlatform --ozone-platform=wayland`
- see the hyprland documentation [how to force electron apps use Wayland](https://wiki.hyprland.org/Getting-Started/Master-Tutorial/#force-apps-to-use-wayland)
- in hyprland you can check if an application is running on xwayland (bridge to X11) using the `hyprctl clients` command; you don't want that and therefore use the above mentioned flags
- the MimeType was added automatically
## Theming
- I'm using AnuPpuccin theme