# io.lines("text.file", "n|l|L|a")

## `"n"` parameter

The `"n"` parameter tells Lua to read the file as **numbers** rather than raw lines of text.

Reddit

In `io.lines(...)`, any format parameters are passed directly to `file:read(...)` under the hood. The `"n"` format (or `"*n"` in older Lua versions) instructs the iterator to:

1. Skip any leading whitespaces or newlines.
2. Read and parse the next valid numerical value (integer or floating-point).
3. Return the value as a Lua `number` type.
4. Stop the loop once it reaches the end of the file and can no longer find a number.

### Example

Suppose `text.txt` contains numbers separated by arbitrary spaces or newlines:

```plaintext
10   20.5
30
40
```

```lua
-- Reads number by number, ignoring layout
for num in io.lines("text.txt", "n") do
    print(num * 2) -- num is guaranteed to be a number type
end

-- Output:
-- 20
-- 41.0
-- 60
-- 80
```

_Note: If the file contains non-numeric text, `io.lines` will return `nil` upon hitting the invalid character and terminate the loop early._

## `"l"` parameter

Iterate through each line of a file without the newlines. The opening and closing of the file is automatic.

```plaintext
First line
Second line
Third line
```

```lua
for line in io.lines("text.txt", "l") do
    print(line)
end
```

**Output:**

```plaintext
First line
Second line
Third line
```

## `"L"` parameter

The same as "l" but keeps the end of line / new line character

```lua
for line in io.lines("text.txt", "l") do
    print(line)
end
```

**Output:**

```plaintext
First line
Second line
Third line
```

## Difference between `io.lines("file", "l")` and `file:read("l")`

The core difference comes down to **control flow** versus **resource lifecycle**: `io.lines()` is a higher-level **iterator** designed for `for` loops that opens and closes the file automatically, while `file:read()` is a single-shot **method** called on an already open file handle.

Here is how they compare across key categories:

| Feature              | `io.lines("file", "l")`                               | `file:read("l")`                                |
| :------------------- | :---------------------------------------------------- | :---------------------------------------------- |
| **Primary Use Case** | Streaming/iterating line-by-line in a loop            | Manual reading from an open file handle         |
| **File Management**  | Opens, reads, and **closes** the file automatically   | Requires manual `io.open()` and `file:close()`  |
| **Return Value**     | An iterator function (yields one line per loop pass)  | A single string (or `nil` at EOF)               |
| **Current Position** | Resets to the start every time `io.lines()` is called | Advances the file position pointer sequentially |
| **Error Handling**   | Raises a Lua error if the file cannot be opened       | Returns `nil, err_msg` when `io.open()` fails   |

### Code Comparison

`io.lines("file", "l")`

```lua
-- Automatically opens "file", yields lines until EOF, and closes the handle
for line in io.lines("file.txt", "l") do
    print(line)
end
```

`file:read("l")`

```lua
local file = assert(io.open("file.txt", "r"))
-- You manually control reading and file closure
local line = file:read("l") -- Reads just ONE line
print(line)
file:close()
```

### Key Caveat

If you pass an **already open file handle** to `io.lines()` instead of a filename path (e.g., `io.lines(file, "l")`), `io.lines` will **not** close the file when the loop ends—you retain ownership of the file handle and must close it yourself.
