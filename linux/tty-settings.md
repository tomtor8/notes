# TTY console settings

## Change the font in TTY

The `setfont` command is used for changing the console fonts.

### Install a custom font

```bash
sudo pacman -S terminus-font
```

### Find your fonts

The console fonts are located at `/usr/share/kbd/consolefonts`.  
The font files en in `gz`.

### Temporarily change the font

```bash
# normal
setfont ter-132n
# bold
setfont ter-132b
```

### Make the change persistent

Edit the `/etc/vsconsole.conf` file.  
Add the following line to the config:

```text
FONT=ter-132n
```
