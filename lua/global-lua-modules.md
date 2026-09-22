# Centralized custom Lua modules

- my custom Lua modules are located at `/home/tom/Code/lua/modules`
- modules from this directory are called using require("modulaname")
- **before calling the require fuction** at the beginning of the file we have to modify the `package.path` at runtime so that the script can correctly load the modules
- you can add more paths to package.path at the same time
- just add another packge.path = package.path .. ......

```lua
local home = os.getenv("HOME")
package.path = package.path .. ";" .. home .. "/Code/lua/modules/?.lua"
```

## How to tell the Lsp to see your custom modules

1. Project-specific `.luarc.json`

The most flexible way is to create a file named .luarc.json in the root of your script's directory. This tells the language server exactly where to look:

```json
{
  "workspace": {
    "library": ["/home/tom/Code/lua/modules"]
  }
}
```

2. AstroNvim / Global solution

If you want these suggestions available in all your scripts, you can modify your AstroNvim configuration (usually in `user/init.lua` or `user/plugins/lsp.lua`) to include that path in the LSP settings:

```Lua
-- Example for lspconfig settings
require('lspconfig').lua_ls.setup {
  settings = {
    Lua = {
      workspace = {
        library = {
          "/home/user/my_lua_modules",
        },
      },
    },
  },
}
```
