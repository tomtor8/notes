# Named varargs tables

**TL;DR:** Named vararg tables in Lua 5.5 eliminate the need to explicitly allocate temporary tables via `table.pack(...)` or `{ ... }` when capturing variable arguments. By specifying a parameter name directly after `...`, Lua automatically generates a read-only array table containing the arguments along with their exact count.

## Key Details & Syntax

In Lua 5.5, you can attach an identifier directly following the `...` parameter in a function definition:

```lua
-- Modern Lua 5.5 named vararg syntax
function process_items(...args)
-- 'args' is automatically bound as a read-only table containing all variadic arguments
 print("Received " .. args.n .. " arguments:")
    for i = 1, args.n do
        print(i, args[i])
    end
end

process_items("apple", nil, "banana")
```

> [!NOTE]
> The `n` attribute is automatically created and hold the length of the table (number of items in the arguments table).

## Comparison: Traditional vs. Lua 5.5

### Traditional ... Expression (Lua 5.1 - 5.4)

Previously, working with ... directly required wrapping it with table.pack(...) or placing it inside { ... } to query indices or preserve trailing nil values:

```lua
function legacy_process(...)
    -- Explicitly create a table with 'n' set to select("#", ...)
    local args = table.pack(...)
    for i = 1, args.n do
        print(i, args[i])
    end
end
```

### Named Vararg Table (...name)

With the new syntax:

- **Automatic Creation:** args is made available directly as a local table without an extra table.pack() call.
- **Immutability:** The variable bound to the vararg table (e.g., args) is **read-only**, preventing unintended re-assignment inside the scope.
- **Handling nil Values:** Like table.pack, the created table includes an .n field containing the total count of passed arguments, ensuring nil values inside or at the end of the vararg list do not break iteration length.

