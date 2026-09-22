# Slicing strings with `string.sub()` function

## The Syntax

The syntax for string.sub() is:
`string.sub(s, i [, j])`

 * `s`: The string you want to slice.
 * `i`: The starting index (Lua indices start at 1, not 0).
 * `j` (optional): The end index. If omitted, it defaults to -1 (the end of the string).

## Key Concepts

 * _1-Based Indexing:_ Unlike Python or C, the first character is at position 1.
 * _Inclusive Range:_ Both the start (i) and end (j) positions are included in the result.
 * _Negative Indices:_ You can count backward from the end of the string. -1 is the last character, -2 is the second to last, etc.

## Examples

**1. Basic Slicing**

To get a specific portion of a string:

```lua
local str = "Neovim is great"
print(string.sub(str, 1, 6))  
-- Output: Neovim
```

**2. Omitting the End Index**

If you only provide the starting position, it captures everything until the end:

```lua
local str = "Arch Linux"
print(string.sub(str, 6))     
-- Output: Linux
```

**3. Using Negative Indices**

This is very helpful for getting file extensions or the end of a path:

```lua
local file = "init.lua"
-- Get the last 3 characters
print(string.sub(file, -3))    
-- Output: lua

-- Get everything except the last 4 characters (.lua)
print(string.sub(file, 1, -5)) 
-- Output: init
```

## Quick Comparison Table

| Command | Result | Why? |
|---|---|---|
| `string.sub("Niri", 1, 1)` | "N" | Start and end at the first character. |
| `string.sub("Niri", 2, -2)` | "ir" | Start at 2nd, end at 2nd to last. |
| `string.sub("Niri", -3)` | "iri" | Start 3 from the end to the very end. |
