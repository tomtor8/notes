# EmmyLua style annotations

## Multiple return values

```lua
---Executes a system command and catches errors via pcall.
---@param cmd string The shell command to execute.
---@param err_msg string The message to throw if the command fails.
---@return boolean success # True if the command ran successfully.
---@return string|nil error # The error message if success is false.
local function exec_cmd(cmd, err_msg)
    local success, err = pcall(function()
        local ok = os.execute(cmd)
        if not ok then
            error(err_msg)
        end
    end)
    return success, err
end
```

`---@return boolean success`: Tells the LSP that the first return value is a toggle for success.

`---@return string|nil error`: The pipe | indicates a "union type." If the function fails, err will be a string. If it succeeds, it will be nil.

Comments (`#`): Adding a description after the # allows Neovim's LSP hover (usually K in AstroNvim) to show exactly what those variables represent.

## Annotate a Module

```lua
---@class MyToolbox
local M = {}

---Executes a command and notifies on failure.
---@param cmd string The shell command.
---@param err_msg string|nil Message for the notification.
---@return boolean|nil ok
function M.exec_cmd(cmd, err_msg)
    -- ... your function logic ...
    return ok
end

return M
```

`@class MyToolbox`: Creates a named type in the LSP's memory.

`local M = {}`: The LSP now treats M as an instance of MyToolbox.

**Cross-file Autocompletion**: When you use `local tools = require("my_toolbox")` in another file, the LSP will know that tools is a MyToolbox type and will suggest exec_cmd automatically.
