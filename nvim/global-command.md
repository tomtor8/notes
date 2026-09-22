## Global command

The global command :g (short for :global) is one of the most powerful built-in tools in Neovim. It lets you search a file for a specific pattern and run a command on every line that matches.
Think of it as a command-line for loop for your text: _"For every line matching X, do Y."_

### The Basic Anatomy

```vim
:g/pattern/command
```

- `:g` — Invokes the global command.
- /pattern/ — The regular expression you are searching for.
- command — The Ex command (command-line mode command) you want to execute on the matching lines. If you leave this blank, it defaults to `:p` (print).

### Common and Useful Examples

Since you are keeping your config plugin-free, mastering built-in Ex commands with :g will save you massive amounts of time.

**Delete empty lines** | `:g/^$/d` | Matches lines that start `^` and immediately end `$`, then executes `:d` (delete).
**Delete commented lines** | `:g/^\s*#/d` | Matches lines starting with optional whitespace and a `#` (like in Fish or Python) and deletes them.
**Invert matching (V-Global)** | `:v/pattern/d` or `:g!/pattern/d` | `:v` (or `:g!`) does the exact opposite. This deletes every line that _does not_ match the pattern.
**Copy lines to the end** | `:g/TODO/t $` | Matches lines with "TODO" and uses the `:t` (target/copy) command to send them to `$` (the end of the file).
**Move lines to a specific place** | `:g/FIXME/m 0` | Matches "FIXME" and uses `:m` (move) to push them to line `0` (the very top of the file).

The following command finds all lines beginning with `100%` and deletes the line containing the match and the following line.

```vim
:g/^100%/d2
```

If you want to delete only the line containing the match:

```vim
:g/<someregex>/d
```

To delete from one line BEFORE to one line AFTER match, use this command:

```vim
:g/regex/.-1,.+1d
```

`.` means the current line, `.-1` starting point one line before the current line, ending point `.+1` one line after the current line

Duplicate all headings beginning with `#` (create a copy of the line that is inserted directly below the matching line). The dot `.` means the line.

```vim
:g/^# /t .
```

If you wanted to copy the line after the matching line PLUS blank line use `g/^# /t .+1` - copy the line after the current line plus one line.

### Advanced Superpowers: Combining with Normal Mode

You aren't restricted to Ex commands. You can pipe standard normal-mode keystrokes into matching lines using the :norm command.

**Comment out all matching lines:**

```vim
:g/local_variable/norm i#
```

This finds every line containing `local_variable`, drops into normal mode, executes `i# ` (inserting a comment character), and hops back out.

**Run a saved macro on specific lines:**
If you recorded a macro into register `q`, you can apply it globally to specific lines:

```vim
:g/regex/norm @q
