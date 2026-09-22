# Neovim Tips and Tricks

## Scrolling the window relative to the current cursor position

`zt` : moves the current line to the top of the window

`zz` : moves the current line to the center of the window

`zb` : moves the current line to the bottom of the window

> [!TIP]
> **Using a Count:** You can prefix the command with a line number to bring a specific line to the top without moving your cursor there first.  
> For example, typing `42zt` will scroll line 42 to the top of your window layout instantly.

## Delete marks

- if you have a mark e.g. `a` and you want to remove the mark
- use the command `delmarks a` or short command `delm a`

## View difference between two files in Vim

- if you have two files and want Neovim’s native side-by-side comparison (which provides syntax highlighting and synchronized scrolling), use the -d flag. This is the Neovim equivalent of the vimdiff command

```sh
# Compare two files
nvim -d file1.txt file2.txt
```

Inside Neovim:

Use `]c` to jump to the ==next change==.
Use `[c` to jump to the previous change.
Use `:diffget` (or `do`) to pull a <u>change</u> from the other buffer.
Use `:diffput` (or `dp`) to push a change to the other buffer.

## Check all available and set parameters of a function

`:lua print(vim.inspect(<some_function>))`

Example:

`:lua print(vim.inspect(vim.diagnostic.config()))`

## Creating Visual Increments (The Number Trick)

1. Create a column of the same number (e.g., all 0 or 00 or 000).
2. Select them with Ctrl-v.
3. Press g followed by Ctrl-a.

**Result:** Neovim will increment them sequentially (0, 1, 2, 3... or 01, 02, 03, 04...).

## Append to Macro

To append new keystrokes to an existing macro in Neovim, you record into the **uppercase** version of that register.

When you use a lowercase letter (like `qa`), Neovim overwrites whatever was previously stored in that register. When you use the uppercase letter (like `qA`), Neovim safely appends your new inputs to the end of the existing macro sequence.

### The Steps

Suppose you already have a macro saved in register **`a`**:

1. In Normal mode, start appending by pressing **`qA`** (Shift + A). _You will see `recording @A` appear in your command line area._
2. Type the extra keystrokes you want to add to the macro.
3. Press **`q`** to stop recording.

Now when you execute your macro with `@a`, it will run the original steps followed immediately by your new additions.

Check the contents of the register with the command `:register a`

### The "Edit Text" Method (Alternative)

If your macro is long or complex, trying to append keystrokes blindly by rerecording can be frustrating if you make a typo. Because registers in Neovim are just blocks of raw text, you can paste the macro directly onto an empty line in your buffer, edit it manually like regular text, and yank it back into the register.

1. In normal mode put the register `a` contents in the buffer `"ap`
2. Edit the register contents
3. Yank back the contents to the register after selecting the text `"ay` or without selection `"ay$`
