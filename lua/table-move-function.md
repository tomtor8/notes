# table.move()

**TLDR:** `table.move(a1, f, e, t [, a2])` copies a range of elements from index f through e in array/table a1, pasting them starting at index t in target table a2 (or a1 itself if omitted). It operates in native C speed and handles overlapping ranges safely.

### Understanding table.move()

Introduced in Lua 5.3, table.move() allows fast, bulk copying of elements between tables or within the same table.

#### **Syntax & Parameters**

`table.move(a1, f, e, t [, a2])`

- **a1**: Source table.
- **f**: First index in a1 to copy from.
- **e**: Ending index (inclusive) in a1 to copy from.
- **t**: Target starting index where copied elements will be written.
- **a2**: _(Optional)_ Target table. If omitted, defaults to a1.
- **Returns**: The destination table (a2 or a1).

## Common Use Cases

### Copying Elements Within the Same Table (Shifting / Overlapping)

`table.move` works safely even when source and target indices overlap.

```lua
local list = {"a", "b", "c", "d"}

-- Shift elements 2..4 to start at index 3 (creates space at index 2)
table.move(list, 2, 4, 3)

-- list is now: {"a", "b", "b", "c", "d"}
```

### Copying a Range Between Two Tables

```lua
local src = {10, 20, 30, 40, 50}
local dest = {}

-- Copy indices 2 through 4 from `src` into `dest` starting at index 1
table.move(src, 2, 4, 1, dest)

-- dest is now: {20, 30, 40}
```

### Concatenating Arrays Efficiently

```lua
local t1 = {"apple", "banana"}
local t2 = {"cherry", "date"}

-- Append all elements of t2 to the end of t1
table.move(t2, 1, #t2, #t1 + 1, t1)

-- t1 is now: {"apple", "banana", "cherry", "date"}
```
