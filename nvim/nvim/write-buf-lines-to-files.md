# Write buffer lines to files

A file may consist of lines of text, where each line is some meaningful record of information. This tip shows how to save each line to a separate file (one line per file).

The following command saves each line in the current buffer to a separate file:

```vim
:g/^/execute '.w '.line('.').'.txt'
```

The first line is saved in file `1.txt`, and the second line is saved in file `2.txt`, and so on. If a file exists, it is not overwritten—the command stops with an error message, and does not attempt to write any further files. If wanted, replace .w with .w! to force files to be overwritten.

The following command is similar, but it only saves non-empty lines (empty lines are skipped):

```vim
:g/./execute '.w '.line('.').'.txt'
```

**Explanation:**

`:g/^/...`

The global command performs a command (shown as ... here) on each line that matches the pattern `^`. Since that pattern matches the start of a line, it matches every line, and whatever command is used instead of `...` will be performed on each line.

`execute ...`

This evaluates the following expression, then executes the result as a command.

`.w`

The `w` is the write command, and the `.` is a range that refers to the current line, so the command writes only the current line.

`line('.').'.txt'`

`line('.')` gives the current line number, which is concatenated with the string .txt.

For the first line, `:g/^/` executes the command `execute '.w 1.txt'` which writes the current line (the first line) to file `1.txt`.

If only some lines are wanted, an appropriate search pattern can be used. For example, the following command operates on all lines that start with "Error". In each case, three lines (the line before "Error", the "Error" line, and the line after) are written to a file named `N.txt` where N is the number of the line containing "Error".

```vim
:g/^Error/execute '.-1,.+1w '.line('.').'.txt'
```

> [!Note]
> `:10,15w test1.txt` writes lines 10 to 15 to the file test1.txt
> `:20,25w >> test1.txt` append lines 20 to 25 to the file test1.txt

