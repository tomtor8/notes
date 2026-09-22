# What to install on Arch Linux

- for vanilla nvim lsp to work you need to install the following

```bash
yay -S shellcheck-bin
sudo pacman -S lua-language-server stylua
sudo pacman -S vscode-html-languageserver
sudo pacman -S vscode-css-languageserver
sudo pacman -S vscode-json-languageserver
sudo pacman -S prettier
sudo pacman -S bash-language-server shfmt
pipx install basedpyright
pipx install ruff
npm -g install fish-lsp
rustup component add rust-analyzer
```

