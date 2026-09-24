# Lua metatables

A **metatable** in Lua is a regular table attached to another table to change or extend its behavior. By defining special keys called **metamethods** (which start with `__`), you can overload operators (`+`, `-`, `==`), handle missing keys, or customize string output.  
Think of a table as your data, and its metatable as its "rulebook."

## Overloading Operators (`__add`)

By default, adding two tables with `+` throws an error. The `__add` metamethod tells Lua how to handle addition for your custom tables.

```lua
-- Define two vector tables
local vec1 = { x = 10, y = 20 }
local vec2 = { x = 5,  y = 15 }

-- Create a metatable with an __add metamethod
local vector_mt = {
    __add = function(a, b)
        -- Return a new table with summed coordinates
        return { x = a.x + b.x, y = a.y + b.y }
    end
}

-- Attach the metatable to both vectors
setmetatable(vec1, vector_mt)
setmetatable(vec2, vector_mt)

-- Now you can use '+' directly
local result = vec1 + vec2
print(result.x, result.y) -- Output: 15  35
```

## Default Values (`__index` as a Table)

When you look up a key that doesn't exist in a table, Lua normally returns nil. If an `__index` metamethod is set, Lua looks inside that fallback table instead.

```lua
local defaults = { hp = 100, speed = 10, mana = 50 }
local player = { hp = 80 } -- Only 'hp' is defined explicitly

-- Tell Lua: if a key is missing in 'player', look in 'defaults'
setmetatable(player, { __index = defaults })

print(player.hp)    -- Output: 80  (found directly in 'player')
print(player.speed) -- Output: 10  (fallback to 'defaults')
print(player.mana)  -- Output: 50  (fallback to 'defaults')
```

## Dynamic Lookups (`__index` as a Function)

`__index` can also be a function, allowing you to compute or log values dynamically when a missing key is accessed.

```lua
local inventory = { apples = 3 }

local mt = {
 __index = function(tbl, key)
 -- Log missing key access or calculate default
 print("Warning: Key '" .. key .. "' not found. Defaulting to 0.")
 return 0
 end
}

setmetatable(inventory, mt)

print(inventory.apples)  -- Output: 3
print(inventory.oranges) -- Prints warning, then Output: 0
```

## Custom String Output (`__tostring`)

Customize what `print()` or `tostring()` outputs instead of getting a raw table memory address like table: 0x....

```lua
local hero = { name = "Aragorn", level = 20 }

setmetatable(hero, {
 __tostring = function(t)
 return string.format("Hero: %s (Level %d)", t.name, t.level)
 end
})

print(hero) -- Output: Hero: Aragorn (Level 20)
```

## Calling Tables Like Functions (`__call`)

The `__call` metamethod allows you to execute a table as if it were a function. This is useful for creating objects with internal state that can be "invoked" directly.

```lua
-- Define a counter table
local counter = { count = 0 }

-- Create a metatable with a __call metamethod
local mt = {
 __call = function(t, increment)
 t.count = t.count + (increment or 1)
 return t.count
 end
}

setmetatable(counter, mt)

-- Call the table directly
print(counter()) -- Output: 1 (increments by default 1)
print(counter(5)) -- Output: 6 (increments by 5)
```

## Controlling Table Updates (`__newindex`)

The `__newindex` metamethod controls how values are assigned to missing or new keys. While normal assignment adds the key directly to the table, `__newindex` lets you intercept these attempts to make tables read-only, log updates, or redirect data.

```lua
code
-- Define a protected table
local target = {}

-- Create a metatable with a __newindex metamethod
local mt = {
 __newindex = function(t, key, value)
 print(string.format("Blocked update: Cannot set '%s' to '%s'", key, tostring(value)))
 end
}

setmetatable(target, mt)

-- Attempt to assign a value
target.score = 100 -- Prints: Blocked update: Cannot set 'score' to '100'
print(target.score) -- Output: nil (key was not added)
```

## Comparing Tables (`__eq`)

By default, Lua compares tables by their memory reference, meaning two tables with identical contents are considered different unless they are the exact same table instance. The  
`__eq` metamethod allows you to define custom equality logic.

```lua
-- Define two player score tables
local p1 = { score = 100 }
local p2 = { score = 100 }

-- Create a metatable with an __eq metamethod
local mt = {
 __eq = function(a, b)
 return a.score == b.score
 end
}

setmetatable(p1, mt)
setmetatable(p2, mt)

-- Note: For __eq to work, both tables must share the same metatable
print(p1 == p2) -- Output: true
```

## Common Metamethods Reference

| Metamethod                  | Trigger Event                                   |
| :-------------------------- | :---------------------------------------------- |
| `__index`                   | Reading a key that doesn't exist                |
| `__newindex`                | Writing to a key that doesn't exist             |
| `__add` / `__sub` / `__mul` | Arithmetic operators (`+`, `-`, `*`)            |
| `__eq` / `__lt` / `__le`    | Comparison operators (`==`, `<`, `<=`)          |
| `__tostring`                | String conversion via `tostring()` or `print()` |
| `__call`                    | Calling the table like a function: `tbl()`      |
