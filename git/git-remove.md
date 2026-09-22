# Git rm command

The `git rm` command is the inverse of `git add` and is used to **remove tracked files** from your Git repository.

Unlike the standard Linux `rm` command, `git rm` performs a dual action:

1.  It **deletes the file** from your working directory (filesystem).
2.  It **stages the deletion** in the Git index (staging area) for the next commit.

---

## 🛠️ Basic Usage

The syntax is straightforward, similar to your regular Linux command line tools:

```bash
git rm <file-name>
```

### Example: Deleting a File

1.  **Before:** You have a file named `old_config.txt` that is currently tracked (in Git).
2.  **Command:**
    ```bash
    git rm old_config.txt
    ```
3.  **Result:**
    - The file `old_config.txt` is **deleted** from your file system.
    - The deletion of the file is **staged**.
4.  **Next Step:** Commit the change to finalize the removal from your repository history.
    ```bash
    git commit -m "Remove obsolete configuration file"
    ```

---

## 💡 Crucial Options

There are two flags you'll use most often with `git rm`, depending on what you want to achieve.

### 1\. `--cached` (Untracking a File)

This is perhaps the most important variation of `git rm`. It removes the file from Git's **tracking** (the index) but **leaves the physical file on your local disk** (in the working directory).

- **Use Case:** You accidentally committed a large file or a sensitive configuration file (like a local `.env` file), and you want to **untrack it** without deleting your local copy. It's often followed by adding the filename to `.gitignore`.

- **Command:**

  ```bash
  git rm --cached sensitive_data.txt
  ```

- **Result:**
  - `sensitive_data.txt` is **removed** from the staging area.
  - The **file remains** in your working directory.
  - The next commit will record its removal from the repository's history.

### 2\. `-r` (Recursive Deletion)

To remove an entire directory and all its contents, you need to use the recursive flag, similar to `rm -r` in the Linux shell.

- **Use Case:** You need to delete a folder like `old_docs/` and everything inside it.

- **Command:**

  ```bash
  git rm -r old_docs/
  ```

### 3\. `-f` (Force Deletion)

Git has a safety mechanism: it won't let you use `git rm` on a file if you have **modified it and haven't committed the changes**. This prevents you from accidentally losing work.

- **Use Case:** You are certain you want to delete the file _and_ discard all the uncommitted changes in it.

- **Command:**

  ```bash
  git rm -f forgotten_file.js
  ```
