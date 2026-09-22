# Custom keybindings in Fuzzel

You can use custom keybindings except the `return`, e.g. `Alt+1`, `Alt+2` and so on.

`pipe:close()` is the key element to get the exit status/code.

## Exit codes

**0** - the selected item in Fuzzel was accepted by pressing `return`
**2** - the selection process was cancelled by pressing `escape`
**10** - the selected item in Fuzzel was accepted by pressing `Alt+1`
**11** - the selected item in Fuzzel was accepted by pressing `Alt+2` and so on

**Test example:**

```lua
local cmd = [[echo -en "First line\nSecond line\nThird line" | fuzzel --dmenu]]

local output
local ok, reason, exit_code

local pipe = io.popen(cmd, "r")
if pipe then
    -- it is necessary to remove the trailing newline of the output
    -- with gsub (see my other scripts)
    output = pipe:read("*a")
    -- pipe:close() returns: (boolean, string exit|signal, number-exit code)
    -- _, _, exit_code = pipe:close() -- catch only exit code
    ok, reason, exit_code = pipe:close()
end

-- Handling the exit status
if not ok then
    -- In Fuzzel, exit code 2 usually means the user pressed Escape/cancelled
    -- Pressing Alt+1 returns exit code 10 and `ok` false
    -- keybinds can be customized in the fuzzel.ini
    -- Alt+2 returns exit code 11 and `ok` and so on
    print("Command failed or was cancelled.")
    print("Reason: " .. reason) -- likely "exit"
    print("Status code: " .. exit_code)
else
    print("Selected: " .. (output or "nothing") .. " with exit code " .. exit_code)
end
```

## If..then logic

You can then use `if..then` logic to apply different actions to the selected option (e.g. open the file, copy the file path to clipboard, remove the file and so on)

**Example:**

```lua
#!/usr/bin/env lua

local home = os.getenv("HOME")
package.path = package.path .. ";" .. home .. "/Code/lua/modules/?.lua"
local t = require("textutils")

local cmd = [[echo -en "First line\nSecond line\nThird line" | fuzzel --dmenu]]

local output
local exit_code

local pipe = io.popen(cmd, "r")
if pipe then
    -- it is necessary to remove the trailing newline of the output
    -- with gsub (see my other scripts)
    output = t.rstrip(pipe:read("*a"))
    -- pipe:close() returns: (boolean, string exit|signal, number-exit code)
    _, _, exit_code = pipe:close()
end

if exit_code == 0 then
    print("The chosen item: " .. output .. " PRINTED")
elseif exit_code == 10 then
    print("You pressed the Alt+1 key combo.")
    print("The output was copied to clipboard")
    os.execute(string.format([[echo "%s" | wl-copy]], output))
elseif exit_code == 11 then
    print("You pressed the Alt+2 key combo.")
    print("The output was copied to clipboard")
    os.execute(string.format([[echo "%s" | wl-copy]], output))
else
    print("pressed something else")
end
```
