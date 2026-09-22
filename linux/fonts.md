## Link existing font downloads in unorthodox locations

- Dank Material Shell downloads its own nerd font `FiraCodeNerdFont` to directory `/usr/share/quickshell/dms/assets/fonts/nerd-fonts/`
- you can link this directory to your `$HOME/.local/share/fonts` directory to access it systemwide

```bash
ln -s /usr/share/quickshell/dms/assets/fonts/nerd-fonts/ ~/.local/share/fonts/
```

## List all available fonts

Use the command:

```bash
fc-list | grep "your-searched-font"
```

- you can try different options with `fc-list --help`
- one useful option is `fc-list -b`

## Update the font cache

- do it after installing a new font

```bash
fc-cache -fv
```
