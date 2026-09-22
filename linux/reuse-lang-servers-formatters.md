# Intro

- you don't have to install language servers and formatters and linters again and again on your system

# Reuse already installed packages

- if you have installed neovim and are using Mason to install servers, formatters and linters
- those are installed here: `/home/tom/.local/share/nvim/mason/bin/`
- the `bin` directory contains e.g. `bash-language-server`, `shfmt`, `shellcheck` and so on...

# Zed editor integration

## bash-language-server

- first install the `bash-language-server` extension in the Zed editor

#### `shfmt` formatter

- in the `~/.config/zed/settings.json` add this to enable e.g. `shfmt` formatter

```json
    "Shell Script": {
      "format_on_save": "on",
      "formatter": {
        "external": {
          "command": "/home/tom/.local/share/nvim/mason/bin/shfmt",
          "arguments": ["--filename", "{buffer_path}", "--indent", "2"]
        }
      }
    }
  }
```

### `shellcheck` linter

- you have to have `shellcheck` installed and have it in your `PATH`
- BUT if you have it installed using Mason in `nvim` then just LINK the executable `shellcheck` file to a directory that is in the `PATH`
- for links ALWAYS use the complete paths

Example:

```bash
ln -s /home/tom/.local/share/nvim/mason/bin/shellcheck /home/tom/bin/shellcheck
```

## Lua

### lua-language-server

- there is an extension for lua support in Zed
- the place where the extensions are located is `/home/tom/.local/share/zed/extensions`
- in my cas the support didn't work
- the following example shows how to apply a local custom lsp

```json
"lsp": {
    "lua-language-server": {
      "binary": {
        "ignore_system_version": false,
        // link to older lua-language-server@3.15.0
        // the latest 3.16.0 version has serious issues as of 5 Dec 2025
        "path": "/home/tom/.local/share/nvim/mason/bin/lua-language-server"
      }
    }
  }
```

### `stylua` formatter

- we are going to reuse the `stylua` downloaded via Mason in neovim
- you can link the stylua formatter to be used in Zed editor like this:

```json
"languages": {
    "Lua": {
      "tab_size": 4,
      "format_on_save": "on",
      "formatter": {
        "external": {
          "command": "/home/tom/.local/share/nvim/mason/bin/stylua",
          "arguments": ["-"]
        }
      }
    }
  }
```

# Neovim editor integration via Mason

## Lua

- if lua-language-server had some issues
- check lsp log files in `~/.local/state/nvim/lsp.log` for lsp errors
- sometimes it helps to revert to the previous version of the language service
- in Mason you can specify the version of the lsp downloaded

```vim
:MasonInstall lua-language-server@3.15.0'
```

### `stylua` formatter

- install stylua as usual using Mason
