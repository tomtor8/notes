# file:read("a|l|L|n")

## `"l"` argument

Reads one line without the end / new line character. The `L` argument reads one line including the end / new line character.

```lua
local file = assert(io.open("file.txt", "r"))

-- You manually control reading and file closure
local line = file:read("l") -- Reads just ONE line
print(line)
local secondline = file:read("l") -- Reads the second line
print(secondline)
file:close()
```

## `"a"` argument

Reads the entire file from the file:seek() actual position. Default it the start of the file.

```lua
-- Idiomatic way to read an entire file into memory:
local f = assert(io.open("text.txt", "r"))
local content = f:read("a")
f:close()
```

## Read file function

```lua
local function read_file_or_default(path, default)
    -- the _ is an error message, we don't use it here
    local f, _ = io.open(path, "r")
    if not f then
        -- returns default file content
        return default
    end
    local content = f:read("a")
    f:close()
    return content
end

-- Usage: Returns fallback string if file is missing
local config = read_file_or_default("missing.txt", "default_setting = true")
```

## `"n"` argument

Reads numbers one by one.

```plaintext
1 2
3
4
```

```lua
local file = assert(io.open("file.txt", "r"))
-- You manually control reading and file closure
local num1 = file:read("n") -- Reads number 1
print(num1)
local num2 = file:read("n") -- Reads number 2
print(num2)
file:close()
```
