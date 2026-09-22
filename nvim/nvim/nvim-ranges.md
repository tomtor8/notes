# Ranges

A _range_ permits a command to be applied to a group of lines in the current buffer. For most commands, the default range is the current line. For example:

- `:s/old/new/g` changes all _old_ to _new_ in the current line
- `:11,15s/old/new/g` changes lines 11 to 15 inclusive
- `:%s/old/new/g` changes all lines

## Examples

For most commands, the default range is `.` (the current line, for example, `:s///` substitutes in the current line). However, for `:g//` and `:w` the default is `%` (all lines).

| Example        | Equivalent      | Description                      |
| -------------- | --------------- | -------------------------------- |
| `:s/old/new/g` | `:.s/old/new/g` | substitute in current line       |
| `:g/old/`      | `:%g/old/`      | list all lines matching `old`    |
| `:w my.txt`    | `:%w my.txt`    | write all lines to file `my.txt` |

## Selections

A command like `:123,145s/old/new/g` substitutes in lines 123 to 145 inclusive, but what if you're not sure what the line numbers are? One method is to use _marks_: Type `ma` in the first line, then type `mb` in the last line (to set marks `a` and `b`). Then enter command `:'a,'bs/old/new/g` to substitute in lines from mark `a` to `b`, inclusive.

Another method is to visually select lines, then enter a colon command (for example, `:s/old/new/g`). Note that you do not enter a range. However, because the command was entered while lines were selected, Vim displays the command as:

```vim
:'<,'>s/old/new/g
```

The range `'<,'>` is entered automatically to identify the lines that were last visually selected (they do not need to be visually selected now).

For example, you might type `vip` to visually select "inner paragraph" (the paragraph holding the cursor). Then type `:s/old/new/g` to substitute in all lines in the selected paragraph.

## Deleting, copying and moving

Ranges work with Ex commands (those typed after a colon, for example, `:w`). As well as the commands we've seen so far, it's handy to know how to use `:d` (delete lines), `:t` or `:co` (copy lines), and `:m` (move lines).

| Command               | Description                                         |
| --------------------- | --------------------------------------------------- |
| `:21,25d`             | delete lines 21 to 25 inclusive                     |
| `:21,25w test.txt`    | write lines 21 to 25 to test.txt                    |
| `:30,35w >> test.txt` | append lines 30 to 35 to test.txt                   |
| `:$d`                 | delete the last line                                |
| `:1,.-1d`             | delete all lines before the current line            |
| `:.+1,$d`             | delete all lines after the current line             |
| `:21,25t 30`          | copy lines 21 to 25 inclusive to just after line 30 |
| `:$t 0`               | copy the last line to before the first line         |
| `:21,25m 30`          | move lines 21 to 25 inclusive to just after line 30 |
| `:$m 0`               | move the last line to before the first line         |

The line numbers in a command are those _before_ the command executes. In the earlier example which moved lines 21..25 to after 30, the "30" refers to the line number before the move occurred.

## Ranges with marks and searches

In a range, a line number can be given as:

- A mark (for example, `'x` is the line containing mark `x`).
- A search (for example, `/pattern/` is the next line matching _pattern_).

When using a mark, it must exist in the current buffer.

| Command               | Description                                                 |
| --------------------- | ----------------------------------------------------------- |
| `:'a,'bd`             | delete lines from mark `a` to mark `b`, inclusive           |
| `:.,'bd`              | delete lines from the current line to mark `b`, inclusive   |
| `:'a,'bm 0`           | move lines from mark `a` to `b` inclusive, to the beginning |
| `:'a,'bw file.txt`    | write lines from mark `a` to `b` to file.txt                |
| `:'a,'bw >> file.txt` | append lines from mark `a` to `b` to file.txt               |

Here are some examples using searches:

```vim
:.,/green/co $
```

Copy the lines from the current line to the next line containing 'green' (inclusive), to the end of the buffer.

```vim
:/apples/,/apples/+1s/old/new/g
```

Replace all "old" in the next line in which the "apples" occurs, and the line following it.

```vim
:/apples/;.1s/old/new/g
```

Same (`.1` is `.+1`, and because `;` was used, the cursor position is set to the line matching "apples" _before_ interpreting the `.+1`).

```vim
:/apples/,.100s/old/new/g
```

Replace all "old" in the next line in which "apples" occurs, and all lines up to and including 100 lines after the current line (where the command was entered).

To do a replace in blocks identified by an initial and a final pattern:

```vim
:/apples/,/peaches/ s/old/new/g
```

Replace all "old" in the first block that starts with "apples" and ends with "peaches".

`/apples/` identifies the first line after the cursor containing "apples".

`/peaches/` is similar (first line after the current line, _not_ the first after "apples"). Be aware of backwards ranges.

The block is all lines from "apples" to "peaches", inclusive.

```vim
:/apples/;/peaches/ s/old/new/g
```

Same, but "peaches" identifies the first occurrence _after_ "apples".

```vim
:/apples/,/peaches/ s/^/# /g
```

Insert "`#` " at the start of each line in the first block.

```vim
:/apples/+1,/peaches/-1 s/^/# /g
```

Insert "`#` " at the start of each line inside the block.

To do a global replace in all blocks with the same patterns, use `:g`:

```vim
:g/apples/,/peaches/ s/^/# /g
```

Insert "`#` " at the start of each line in all identified blocks.

`:g/apples/` identifies each line containing "apples".

In each such line, `.,/peaches/ s/^/# /g` is executed

(the `.` is assumed; it means the current line, where "apples" occurs).

Comment out with `# ` everything between `{` and `}`:

```vim
:g/{/+1,/}/-1 s/^/# /g
```
The range here begins one line after `{` and ends one line before `}`.

```vim
:g/^function!\? \(s:\)\?My/;/^endfunction/s/^/" /
```

_This example is for a Vim script where functions start with `function` or `function!` and end with `endfunction`._

Insert "`"` " at the start of each line in each block.

All functions that start with `function My` or `function s:My` will be commented out.

The last line in each block is where `endfunction` first occurs (at the left margin), after where `function My` is found.

Summary:

| Item        | Description                                                    |
| ----------- | -------------------------------------------------------------- |
| `/pattern/` | next line where _pattern_ matches                              |
| `?pattern?` | previous line where _pattern_ matches                          |
| `\/`        | next line where the previously used search pattern matches     |
| `\?`        | previous line where the previously used search pattern matches |
| `\&`        | next line where the previously used substitute pattern matches |
| `0;/that`   | first line containing "that" (also matches in the first line)  |
| `1;/that`   | first line after line 1 containing "that"                      |

## Understanding the semicolon `;` in Vim ranges

To understand the semicolon (`;`) in Vim ranges, we first need to look at how a standard comma-separated range works, and then see how the semicolon changes Vim's internal "focus."

Here is the breakdown of how ranges handle line numbers and why the semicolon is a crucial tool.

### The Standard Range (Using `,`)

In a standard range like `:10,20d`, Vim interprets both numbers relative to your **current cursor position** before the command runs.

If you use relative modifiers (like `+` or `-`), they both calculate their starting point from where your cursor is right now.

- **Example:** `:.,+5d` means "delete from the current line (`.`) to 5 lines below the current line."
- Both sides of the comma are calculated **simultaneously** based on the initial cursor position.

### Why the Semicolon (`;`) is Used

The semicolon changes the **context** of the second half of the range.

When you use `;` instead of `,`, Vim jumps its internal "telemetry" to the first mark _before_ it calculates the second mark. In technical terms, **it sets the current line (`.`) to the first address before interpreting the second address.**

> **Formula:** `[start];[end]`
>
> 1. Find `[start]`.
> 2. Temporarily move the cursor's context to `[start]`.
> 3. Calculate `[end]` relative to that _new_ position.

### Practical Example: Why it Matters

Imagine your cursor is currently on **line 50**, and you want to find the next occurrence of the word `fuzzel`, and then delete from that match down 5 lines.

#### The Wrong Way (Using Comma):

```vim
:/fuzzel/,+5d
```

- **What Vim does:** It finds the next `fuzzel` (let's say it's on line 80). Then it looks at `+5`. Because a comma was used, `+5` is calculated from your _original_ position (line 50).
- **The Result:** It tries to delete from line 80 to line 55 (`50 + 5`). This causes a `Backward range given` error or unexpected behavior.

#### The Right Way (Using Semicolon):

```vim
:/fuzzel/;+5d
```

- **What Vim does:** It finds the next `fuzzel` (line 80). Because of the semicolon, Vim now pretends your cursor is on line 80. It then evaluates `+5` relative to line 80.
- **The Result:** It successfully deletes from line 80 to line 85.

### Summary of Differences

`[addr1],[addr2]` : Both addresses are calculated from the **original** cursor position. Absolute line numbers or ranges relative only to where you are right now.
`[addr1];[addr2]` : `[addr2]` is calculated from the location of **`[addr1]`**. Searching for a pattern/mark first, then modifying a specific number of lines relative to that match.
