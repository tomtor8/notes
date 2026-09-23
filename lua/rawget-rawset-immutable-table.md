# rawget()

**TL;DR:** `rawget(table, index)` fetches the value stored directly in a Lua table key without invoking any `__index` metamethod that might be attached to it.

## How rawget Works

When you access a table value using standard indexing like `t[key]`, Lua checks if key exists in table t. If it doesn't, Lua looks for a metatable attached to t and triggers the `__index` metamethod (if defined).

`rawget(table, index)` skips the metatable lookup entirely and performs a direct, raw access.

```lua
-- Create a base fallback table
local fallback = { default_key = "default_value" }

-- Create a main table with a metatable attached
local t = { real_key = "real_value" }
setmetatable(t, { __index = fallback })

-- Standard indexing triggers the __index fallback:
print(t.default_key)        -- Output: "default_value"

-- rawget ignores __index and checks only the raw table 't':
print(rawget(t, "default_key")) -- Output: nil
print(rawget(t, "real_key"))    -- Output: "real_value"
```

## Key Use Cases

1. **Preventing Infinite Loops in Metamethods:** Inside custom **index function definitions, using standard indexing like t[k] triggers **index again, causing infinite recursion. rawget(t, k) safely checks the underlying table.
2. **Performance:** Bypassing metatable checks when you need to confirm if a table directly contains a key.
3. **Internal Lib/Plugin Logic:** Useful when building Neovim/Lua modules or UI configurations where metatables wrap tables to handle defaults or dynamic fallbacks.

---

# rawset()

**TL;DR:** `rawset(table, key, value)` sets a key-value pair directly on a table while bypassing any `__newindex` metamethod, pairing with rawget to read/write data safely without triggering custom hooks or infinite loops.

## How rawset Works Alongside rawget

When you assign a value to a non-existent table key using standard syntax (`t[key] = value`), Lua triggers the `__newindex` metamethod if one exists.

`rawset(table, key, value)` assigns the value directly to the table, ignoring `__newindex`.

Here is how both functions work together to create a proxy or safe tracking wrapper:

```lua
local data = { count = 10 }
local proxy = {}

-- Track writes and reads via metatable
local mt = {
    __index = function(t, key)
        print("-> Reading key: " .. tostring(key))
        -- Use rawget to read from 'data' without triggering loop/metamethods
        return rawget(data, key)
    end,

    __newindex = function(t, key, value)
        print("-> Writing key: " .. tostring(key) .. " = " .. tostring(value))
        -- Use rawset to write directly to 'data' bypassing __newindex
        rawset(data, key, value)
    end
}

setmetatable(proxy, mt)

-- Writing through the proxy triggers __newindex and uses rawset underneath:
proxy.count = 11      -- Output: -> Writing key: count = 11
proxy.status = "ok"   -- Output: -> Writing key: status = ok

-- Reading through the proxy triggers __index and uses rawget underneath:
print(proxy.count)    -- Output: -> Reading key: count
--         11

-- Verify direct state in original data table
print(data.status)    -- Output: ok
```

## Key Comparisons

| Function          | Standard Equivalent | Bypasses Metamethod | Primary Use Case                    |
| :---------------- | :------------------ | :------------------ | :---------------------------------- |
| `rawget(t, k)`    | `t[k]` (Read)       | `__index`           | Safe reading / preventing recursion |
| `rawset(t, k, v)` | `t[k]` = v (Write)  | `__newindex`        | Safe writing / raw mutation         |

---

# Implement an immutable table

**TL;DR:** To build an immutable table in Lua, return a empty proxy table wrapped in a metatable where `__index` uses rawget to fetch values from a hidden table, and `__newindex` throws an error to block modifications.

## Implementation

The standard Lua pattern for a read-only table uses a hidden data table wrapped inside a blank proxy table. Any read operation falls back to `__index`, while any write operation triggers `__newindex` and raises an error.

```lua
local function make_readonly(target_table)
    local proxy = {}

    local mt = {
        -- Pass-through read operations via rawget
        __index = function(_, key)
            return rawget(target_table, key)
        end,

        -- Intercept and block write operations
        __newindex = function(_, key, value)
            error("Attempt to modify read-only table at key: " .. tostring(key), 2)
        end,

        -- Optional: Protect the metatable from being changed or retrieved via getmetatable
        __metatable = "This metatable is locked."
    }
     return setmetatable(proxy, mt)
end

-- Example Usage
local config = make_readonly({
 host = "127.0.0.1",
 port = 8080,
 debug = true
})

-- Reading values works as expected:
print(config.host) -- Output: 127.0.0.1
print(config.port) -- Output: 8080

-- Attempting to write throws an error:
config.port = 9090
-- Output: lua: example.lua:26: Attempt to modify read-only table at key: port
```

## Bypassing Read-Only Restrictions with rawset

If you need an internal system function that is allowed to bypass the read-only constraint (e.g., during initialization or dynamic updates), use rawset directly on the underlying target_table:

```lua
local function update_internal(readonly_proxy, hidden_table, key, value)
    -- Bypasses the proxy's __newindex entirely
    rawset(hidden_table, key, value)
end
```

## Key Technical Details

7. **The Empty Proxy:** The proxy table **must** remain empty. If keys exist directly inside proxy, Lua reads and writes them directly without triggering `__index` or `__newindex`.
8. **The `__metatable` Protection:** Setting `__metatable = "Locked"` prevents external code from running `setmetatable(proxy, {})` to strip away the read-only protection.
9. **Error Level 2:** Using `error(msg, 2)` points the line number in the stack trace to the location where the caller attempted the illegal modification, rather than inside the `__newindex` function itself.
