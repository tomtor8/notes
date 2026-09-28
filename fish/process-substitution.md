# Process substitution

**TLDR:** Process substitution (`psub`) allows a command's output to be treated as a temporary file/pipe. Running `diff (sort a.txt) (sort b.txt)` fails in Fish because command substitution `(...)` evaluates to raw text strings, causing `diff` to look for non-existent files named after the sorted line contents.

## How Process Substitution Works (`psub`)

In the **Fish shell**, process substitution is implemented via the `psub` function.

```fish
diff (sort a.txt | psub) (sort b.txt | psub)
```

1.  **Pipeline Execution:** `sort a.txt` runs and pipes its stdout into `psub`.
2.  **Pipe/FIFO Creation:** `psub` creates a named pipe (FIFO) or temporary file in `/tmp` (e.g., `/tmp/psub.XXXXXX`).
3.  **Path Replacement:** `psub` outputs the **file path** of that pipe/file back to Fish.
4.  **Command Execution:** The outer `diff` command receives the paths as arguments:

```fish
diff /tmp/psub.12345 /tmp/psub.67890
```

5.  **Cleanup:** Once `diff` finishes reading, Fish automatically removes the temporary pipe/file.

## What Happens Without `psub`?

If you run:

```fish
diff (sort a.txt) (sort b.txt)
```

In Fish, parenthesis `(...)` perform **command substitution**. Fish executes `sort a.txt`, collects all stdout lines, splits them into a list of strings, and passes those raw strings as literal positional arguments to `diff`.

### Example Scenario:

Suppose `a.txt` contains `apple\nbanana` and `b.txt` contains `apple\ncherry`.

- `sort a.txt` outputs `apple` and `banana`.
- `sort b.txt` outputs `apple` and `cherry`.

Fish evaluates your command as:

```fish
diff apple banana apple cherry
```

`diff` expects file paths as arguments. It will attempt to open files named `apple`, `banana`, `apple`, and `cherry` in your current working directory and output an error:

```plaintext
diff: apple: No such file or directory
diff: banana: No such file or directory
```

## Comparing Fish (`psub`) vs. Bash (`<()`)

In Bash/Zsh, process substitution uses the `<(...)` syntax built directly into the parser:

```bash
# Bash / Zsh
diff <(sort a.txt) <(sort b.txt)

# Fish Shell
diff (sort a.txt | psub) (sort b.txt | psub)
```

Both achieve the same result under the hood (passing `/dev/fd/X` or `/tmp/psub.X` paths to `diff`), but Fish uses a pipe function (`psub`) rather than custom syntax symbols.
