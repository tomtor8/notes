# Git mv command

The `git mv` command is used to **move or rename a file, a directory, or a symbolic link** within your Git repository and automatically stages the change for commit.

---

## Key Purpose

When you rename or move a tracked file using your system's shell command (like `mv` in Linux/macOS) or a file explorer, Git sees it as the **old file being deleted** and a **new, untracked file being added**.

The `git mv` command streamlines this by performing three actions in a single step, which tells Git explicitly that the change is a **rename** or **move**:

1.  Renames the file in your working directory (just like the shell `mv` command).
2.  Stages the deletion of the original file path (like `git rm <old-path>`).
3.  Stages the addition of the file at the new path (like `git add <new-path>`).

By doing this, `git mv` ensures that the file's **history is preserved**, allowing Git to clearly track the file across its new location or name in the repository's log.

---

## Basic Usage and Examples

The command syntax is:

```bash
git mv <source> <destination>
```

### 1\. Renaming a File

To rename a file, you provide the old name and the new name in the same directory.

- **Command:** `git mv index.js application.js`
- **Result:** The file `index.js` is renamed to `application.js`, and this rename is staged.
- **`git status` output:**
  ```
  Changes to be committed:
    (use "git restore --staged <file>..." to unstage)
          renamed:    index.js -> application.js
  ```

### 2\. Moving a File

To move a file to a new directory, you provide the file path and the new directory path.

- **Command:** `git mv application.js src/application.js`
- **Result:** The file `application.js` is moved into the existing `src/` directory, and this move is staged.

### 3\. Renaming and Moving a File

You can also do both at once by providing the new path with the new filename.

- **Command:** `git mv old-docs/file.txt new-docs/guide.txt`
- **Result:** The file is moved from `old-docs/file.txt` to `new-docs/guide.txt` and is renamed in the process.

---

## Useful Options

Here are a few common flags you can use with `git mv`:

| Option                      | Description                                                                                                                                 |
| :-------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| **`-f`** or **`--force`**   | Forces the move or rename, even if the destination already exists. **Use with caution** as it will overwrite the existing destination file. |
| **`-n`** or **`--dry-run`** | Shows what would happen without actually performing the move or rename.                                                                     |
| **`-v`** or **`--verbose`** | Reports the names of the files as they are moved.                                                                                           |
