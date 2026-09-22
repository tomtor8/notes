# Usage

- important for Appimages
- created in `~/.local/share/applications`

**Examples**

1. Mochi

`mochi.desktop` file

```
[Desktop Entry]
Name=Mochi
Exec=/home/tom/AppImages/Mochi-1.19.6.AppImage
Terminal=false
Type=Application
Icon=/home/tom/.local/share/icons/Reversal-green-dark/apps/scalable/media-cdrom.svg
Comment=Flashcards App
Categories=Media;
```

2. Obsidian

`obsidian.desktop` file

```
[Desktop Entry]
Name=Obsidian
Exec=env OBSIDIAN_USE_WAYLAND=1 obsidian -enable-features=UseOzonePlatform -ozone-platform=wayland %U
Terminal=false
Type=Application
Icon=obsidian
StartupWMClass=obsidian
Comment=Obsidian
MimeType=x-scheme-handler/obsidian;
Categories=Office;
```

3. Upnotes

`upnotes.desktop` file

```
[Desktop Entry]
Name=UpNote
Exec=/home/tom/AppImages/UpNote.AppImage --enable-features=UseOzonePlatform --ozone-platform=wayland
Terminal=false
Type=Application
Icon=/home/tom/AppImages/upnote_logo.svg
Comment=Note App
Categories=Office;
```
