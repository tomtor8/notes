# Git clean command

The `git clean` command is used to **physically remove untracked files** from your working directory.

This command is one of the most destructive in Git, as the files it deletes are **permanently removed** from your file system, bypassing the trash bin—much like the powerful `rm` command in Linux. Because of this, Git requires you to use specific flags to confirm the operation.

---

## How `git clean` Works

| Key Concept | `git clean` Action                                                                                                                                                                                                             |
| :---------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Target**  | **Untracked files**—files that exist in your working directory but have **never** been added to the Git index with `git add` and are not currently ignored.                                                                    |
| **Purpose** | To clear out build artifacts, temporary logs, downloaded dependencies, or any other file clutter that is not part of your version-controlled project. This is often done to ensure a clean build or before switching branches. |
| **Safety**  | It **does not** touch: tracked files, staged files, or committed files.                                                                                                                                                        |

## Essential Flags for Safe Deletion

Because of the destructive nature of `git clean`, you will almost always use it with one or more of these flags:

### 1\. **`-n` or `--dry-run`** (The Safety Check)

This is the **most important flag**. It shows you exactly which files and directories _would_ be removed without actually deleting anything. You should always run this first\!

```bash
git clean -n
# Output: Would remove my-temp-file.log
```

### 2\. **`-f` or `--force`** (The Go-Ahead)

Git requires the force flag to perform the actual deletion of untracked files.

```bash
git clean -f
# Output: Removing my-temp-file.log
```

### 3\. **`-d`** (Including Directories)

By default, `git clean` only removes untracked files, leaving untracked directories (and their contents) alone. Use `-d` to include untracked directories in the cleanup.

```bash
git clean -fd  # Removes untracked files AND untracked directories
```

### 4\. **`-x`** (Ignoring `gitignore` Rules)

By default, `git clean` **ignores files listed in your `.gitignore`**. If you want to remove _everything_ that isn't tracked, including build directories and dependency folders (like `node_modules/` or `target/`), use `-x`.

```bash
git clean -fx  # Removes untracked files AND files listed in .gitignore
```

## ⚖️ `git clean` vs. `git reset --hard`

It's helpful to remember that **`git reset`** and **`git clean`** are complementary for a total workspace reset:

| Command            | Files Affected                                     | Effect                                                                                   |
| :----------------- | :------------------------------------------------- | :--------------------------------------------------------------------------------------- |
| `git reset --hard` | **Tracked Files** (Staged & Unstaged)              | Discards all local changes to tracked files and reverts them to the last commit's state. |
| `git clean -fd`    | **Untracked Files** (Files Git doesn't know about) | **Deletes** all untracked files and directories.                                         |

To get a truly pristine working directory, you often run both:

```bash
git reset --hard && git clean -fd
```
