# file:seek()

`file:seek()` sets and gets the current read/write position (file pointer) in an open file. Its basic syntax is `file:seek([whence] [, offset])`:

- **`whence`** string options:  
  - `"set"`: base offset from the **start** of the file (default offset is 0).  
  - `"cur"`: base offset from the **current** position in the file.  
  - `"end"`: base offset from the **end** of the file.  
- **`offset`**: integer byte offset (defaults to `0`).  
- **Returns**: the new file position in bytes from the start of the file, or `nil, err` on failure.

When using `file:seek()` with updating modes (`"r+"` and `"a+"`), the key difference lies in how operating systems enforce write restrictions.

## Behavior in `"r+"` Mode (Read & Write / Overwrite)

In `"r+"` mode, you have complete control over the file pointer for **both reads and writes**.

- **Seeking Works Fully:** You can seek to any position in the file to read or overwrite bytes.  
- **Overwriting:** Writing at an offset overwrites existing bytes from that position onward—it does **not** insert text or truncate the rest of the file unless you explicitly write past the current file length.  
- **Requirement to Seek:** Standard C/Lua I/O stream rules recommend calling `file:seek()` (or flushing) when switching back and forth between reading and writing operations to sync internal stream buffers.

local f \= assert(io.open("test.txt", "r+")) \-- text: "Hello World"

\-- Move to byte 6 (after "Hello ")

f:seek("set", 6\)

\-- Overwrites "World" with "Lua\!\!"

f:write("Lua\!\!")

\-- Reset pointer to start and read back

f:seek("set", 0\)

print(f:read("a")) \-- Output: "Hello Lua\!\!"

f:close()

## Behavior in `"a+"` Mode (Read & Append)

In `"a+"` mode, reading and seeking behave differently than writing due to OS-level append restrictions (`O_APPEND`).

- **Seeking Affects Reads:** You can use `file:seek()` to move the pointer anywhere in the file for **reading**.  
- **Writes Ignore Seek Position:** Regardless of where you move the file pointer with `file:seek()`, any subsequent `file:write()` operation **automatically forces the file pointer to the very end of the file** before writing.

local f \= assert(io.open("test.txt", "a+")) \-- text: "Hello World"

\-- Seek to start to read

f:seek("set", 0\)

print("First line read:", f:read("l")) \-- Output: "Hello World"

\-- Try to seek back to byte 0 and write

f:seek("set", 0\)

f:write("START: ") \-- Forcefully appends to END, ignoring pointer at 0\!

\-- Check position after write

print("Position after write:", f:seek("cur")) \-- Output: 18 (end of file)

f:seek("set", 0\)

print(f:read("a")) \-- Output: "Hello WorldSTART: "

f:close()

### Summary Comparison

| Mode | `file:seek()` Affects Reads? | `file:seek()` Affects Writes? | Overwrite Behavior |
| :---- | :---- | :---- | :---- |
| `"r+"` | Yes | **Yes** | Overwrites bytes at the seek position. |
| `"a+"` | Yes | **No** (forced to EOF) | Always appends to the end of the file. |

## Check if a file is empty

local function is\_file\_empty(file\_path)

    local f \= io.open(file\_path, "r")

    if not f then

        return nil, "Could not open file"

    end

    \-- Move file pointer to the end and get total byte length

    local size \= f:seek("end")

    f:close()

    return size \== 0

end

\-- Usage:

local empty, err \= is\_file\_empty("text.txt")

if empty \== true then

    print("The file is empty\!")

elseif empty \== false then

    print("The file is not empty.")

else

    print("Error:", err)

end  
