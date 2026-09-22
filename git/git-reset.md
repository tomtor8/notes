# Git reset command

**`git reset`** is a powerful command used primarily to **undo commits** by moving the current branch pointer (HEAD) to a specified commit.

It is often described as manipulating the "three trees" of Git:

1.  **HEAD** (Commit History): The pointer to the latest commit on your current branch.
2.  **Index** (Staging Area): Where you stage changes with `git add` for the next commit.
3.  **Working Directory** (Working Tree): Your local file system where you edit files.

The different modes of `git reset` determine how far back the command rolls back those three trees.

---

## The Three Modes of `git reset`

When you run `git reset <commit>`, the `<commit>` argument specifies the point in history you want to revert _to_. The three modes determine what happens to the changes that were in the commits you are moving past.

| Mode          | Command (Example)          | HEAD (Commit History)   | Index (Staging Area)          | Working Directory             | Effect on Changes                                                                                                                 |
| :------------ | :------------------------- | :---------------------- | :---------------------------- | :---------------------------- | :-------------------------------------------------------------------------------------------------------------------------------- |
| **`--soft`**  | `git reset --soft HEAD~1`  | **Moves** to `<commit>` | **Keeps** changes staged      | **Keeps** files unchanged     | Undoes the last commit(s), but keeps all the work _staged_ and ready to be re-committed.                                          |
| **`--mixed`** | `git reset --mixed HEAD~1` | **Moves** to `<commit>` | **Resets** (Unstages changes) | **Keeps** files unchanged     | Undoes the commit(s) and _unstages_ the changes, leaving them as local, uncommitted modifications. **(This is the default mode)** |
| **`--hard`**  | `git reset --hard HEAD~1`  | **Moves** to `<commit>` | **Resets** (Discards changes) | **Resets** (Discards changes) | **Discards everything** from the dropped commit(s). This is **DANGEROUS** as it removes uncommitted local changes permanently.    |

> **Note on `HEAD~1`:** `HEAD~1` refers to the commit _before_ the current HEAD (the last commit). This is the most common way to undo the previous commit. You can use `HEAD~N` to go back N commits, or use a specific commit hash.

---

## Common Use Cases

### 1\. Unstaging Files (Without a Commit)

If you only use `git reset` without a commit reference, it defaults to the `--mixed` mode, but it uses the current `HEAD` as the target commit.

- **Goal:** Unstage files you added with `git add` but keep your local file changes.
- **Command:** `git reset` (or `git reset --mixed`)
  - _Result:_ This makes your Index match HEAD, effectively unstaging all changes. The files in your Working Directory are unaffected and remain as unstaged modifications.

### 2\. Undoing the Last Commit to Re-do It (`--soft`)

- **Goal:** Undo a commit because you forgot a file or had a typo in the message, but you want to keep the work staged.
- **Command:**
  ```bash
  git reset --soft HEAD~1
  git commit --amend # Now you can fix the commit message or add more files
  ```

### 3\. Completely Discarding Work (`--hard`)

- **Goal:** You realize the last few hours/days of work are a complete dead-end and you want to completely revert your branch and files to a known good state.
- **Command (Use with Extreme Caution):**
  ```bash
  git reset --hard <commit-hash> # Or HEAD~N
  ```
  This is the equivalent of deleting your work and starting from a fresh checkout of the older commit—it's fast, but unforgiving.
