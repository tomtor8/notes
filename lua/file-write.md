# file.write("Content to be written")

## Append

First line

Second line

local f \= io.open("text.txt", "a") \-- append mode

if not f then

    print("file not found")

    os.exit(1)

end

f:write("Another line")

f:close()

**Output:**

First line

Second line

Another line

## io.open() different modes

The file open modes in Lua determine three things: **stream access** (read-only, write-only, or read-write), **file truncation** (whether existing content is wiped), and **stream positioning** (where writing starts).

Here is the breakdown of how each mode behaves:

| Mode | Read/Write | File Exists? | Wipes Content? | Initial File Pointer |
| :---- | :---- | :---- | :---- | :---- |
| `"r"` | Read only | Must exist (`nil` if missing) | No | Start of file |
| `"w"` | Write only | Created if missing | **Yes** (truncates to 0\) | Start of file |
| `"a"` | Write (Append) | Created if missing | No | **End of file** |
| `"r+"` | Read \+ Write | Must exist (`nil` if missing) | No | Start of file |
| `"w+"` | Read \+ Write | Created if missing | **Yes** (truncates to 0\) | Start of file |
| `"a+"` | Read \+ Append | Created if missing | No | **End of file** for writes |

### Detailed Behavior

- **`"r"` (Read)** Opens a file for reading. Fails and returns `nil, err` if the file does not exist.  
- **`"w"` (Write)** Opens a file for writing. Creates a new file if it doesn't exist, or **overwrites/truncates** an existing file immediately upon opening.  
- **`"a"` (Append)** Opens a file for writing in append mode. Preserves existing content; all write operations automatically go to the end of the file.  
- **`"r+"` (Update Read)** Opens an existing file for both reading and writing without wiping it. Writing overwrites bytes from the current file position rather than truncating the file. Fails if the file does not exist.  
- **`"w+"` (Update Write)** Opens a file for both reading and writing, but **clears all existing data** first. Creates the file if it does not exist.  
- **`"a+"` (Update Append)** Opens a file for both reading and appending. You can read anywhere by repositioning with `file:seek()`, but **all write operations are forced to the end of the file**, regardless of the file pointer position.

