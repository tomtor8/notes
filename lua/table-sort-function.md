# table.sort() function

**TLDR:** `table.sort(tbl [, comp])` sorts an array-like table in-place using Quicksort. It takes an optional comparison function comp(a, b) that must return true if a should come _before_ b. By default, it sorts values in ascending order (a < b).

## Basic Usage: Default Ascending Sort

Without a second argument, table.sort orders elements from lowest to highest using the standard `<` operator.

```lua
local numbers = {5, 2, 8, 1, 9}

-- Sorts the table directly in-place
table.sort(numbers)

-- Output: 1, 2, 5, 8, 9
for _, val in ipairs(numbers) do
    print(val)
end
```

## Custom Sorting: Descending Order

Pass a custom comparator function to change the sorting order. Return true when the first element should sit ahead of the second.

```lua
local scores = {45, 92, 12, 78, 100}

-- Custom closure returning true if 'a' is strictly greater than 'b'
table.sort(scores, function(a, b)
    return a > b
end)

-- Output: 100, 92, 78, 45, 12
for _, score in ipairs(scores) do
    print(score)
end
```

## Advanced Usage: Sorting Objects/Tables

When sorting a list of tables (e.g., objects with key-value pairs), pass a comparison function that evaluates a specific property.

```lua
local players = {
    { name = "Alice", score = 150 },
    { name = "Bob", score = 420 },
    { name = "Charlie", score = 200 }
}

-- Sort players by score descending
table.sort(players, function(a, b)
    return a.score > b.score
end)

-- Output: Bob (420), Charlie (200), Alice (150)
for _, p in ipairs(players) do
    print(string.format("%s: %d", p.name, p.score))
end
```

## Critical Caveats

1. **Strict Weak Ordering Rule:** Your custom comparator comp(a, b) MUST return false when a == b. Returning true for equal values violates strict weak ordering and can cause table.sort to crash with an "invalid order function" error or produce undefined behavior.
   - **Correct:** `return a.score > b.score`
   - **Incorrect:** `return a.score >= b.score` _(Will crash on identical values!)_

2. **Dense Arrays Only:** table.sort expects a sequence (a 1-indexed table without holes/nil values). It will ignore key-value pairs that use non-integer keys.
3. **In-place Mutation:** It does not return a new table; it modifies the original table directly.

### **Practical Tip for Lua Developers**

When sorting strings with tie-breakers, you can chain logical operators directly inside the comparison closure:

```lua
local items = {
    { name = "Sword", level = 5 },
    { name = "Shield", level = 5 },
    { name = "Axe", level = 10 }
}

-- Sort primarily by level descending; secondarily by name ascending
table.sort(items, function(a, b)
    if a.level == b.level then
        return a.name < b.name
    end
    return a.level > b.level
end)
```
