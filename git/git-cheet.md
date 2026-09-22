# Git Cheat Sheet

## Getting Started

Commands to initialize a new repository or clone an existing one.

- **Start a new repo**
  ```bash
  git init
  ```
- **Clone an existing repo**
  ```bash
  git clone <url>
  ```

---

## Prepare to Commit (Staging)

Commands to manage files in your working directory and the staging area (index).

- **Add untracked file or unstaged changes**
  ```bash
  git add <file>
  ```
- **Add all untracked files and unstaged changes**
  ```bash
  git add .
  ```
- **Choose which parts of a file to stage**
  ```bash
  git add -p
  ```
- **Move file**
  ```bash
  git mv <old> <new>
  ```
- **Delete file**
  ```bash
  git rm <file>
  ```
- **Tell Git to forget about a file without deleting it**
  ```bash
  git rm --cached <file>
  ```
- **Unstage one file**
  ```bash
  git reset <file>
  ```
- **Unstage everything**
  ```bash
  git reset
  ```
- **Check what you added (show status)**
  ```bash
  git status
  ```

---

## Make Commits

Commands to record your staged changes into the repository history.

- **Make a commit (opens text editor for message)**
  ```bash
  git commit
  ```
- **Make a commit with an inline message**
  ```bash
  git commit -m 'message'
  ```
- **Commit all tracked/modified changes (bypasses `git add` for tracked files)**
  ```bash
  git commit -am 'message'
  ```

---

## Move Between Branches

Commands for viewing, creating, and switching between branches.

- **Switch branches**
  ```bash
  git switch <name>
  # OR (older command)
  git checkout <name>
  ```
- **Create a branch and switch to it**
  ```bash
  git switch -c <name>
  # OR (older command)
  git checkout -b <name>
  ```
- **List branches**
  ```bash
  git branch
  ```
- **List branches by most recently committed to**
  ```bash
  git branch --sort=-committerdate
  ```
- **Delete a branch**
  ```bash
  git branch -d <name>
  ```
- **Force delete a branch (even if unmerged)**
  ```bash
  git branch -D <name>
  ```

---

## Combine Diverged Branches

Commands for integrating changes from one branch into another.

| Method                                      | Command Sequence                                                     | Description                                                                                 |
| :------------------------------------------ | :------------------------------------------------------------------- | :------------------------------------------------------------------------------------------ |
| **Combine with rebase**                     | `git switch banana` <br> `git rebase main`                           | Moves your branch's commits to the tip of the target branch, creating a linear history.     |
| **Combine with merge**                      | `git switch main` <br> `git merge banana`                            | Creates a new commit (a merge commit) to combine histories.                                 |
| **Combine with squash merge**               | `git switch main` <br> `git merge --squash banana` <br> `git commit` | Combines all of the target branch's changes into a single commit on the destination branch. |
| **Fast-forward merge**                      | `git switch main` <br> `git merge banana`                            | Used when the current branch hasn't diverged; simply moves the pointer forward.             |
| **Copy one commit onto the current branch** | `git cherry-pick <commit>`                                           | Applies a specific commit from another branch onto the current one.                         |

---

## Discard Your Changes

Commands to undo changes in the working directory or staging area.

- **Delete unstaged changes to one file**
  ```bash
  git restore <file>
  # OR
  git checkout <file>
  ```
- **Delete all staged and unstaged changes to one file**
  ```bash
  git restore --staged --worktree <file>
  # OR
  git checkout HEAD <file>
  ```
- **Delete all staged and unstaged changes**
  ```bash
  git reset --hard
  ```
- **Delete untracked files**
  ```bash
  git clean
  ```
- **Temporarily save (stash) all staged and unstaged changes**
  ```bash
  git stash
  ```

---

## Diff Changes and History

Commands to compare changes between different parts of the repository.

- **Diff all staged and unstaged changes**
  ```bash
  git diff HEAD
  ```
- **Diff just staged changes**
  ```bash
  git diff --staged
  ```
- **Diff just unstaged changes**
  ```bash
  git diff
  ```
- **Show diff between a commit and its parent**
  ```bash
  git show <commit>
  ```
- **Diff two commits**
  ```bash
  git diff <commit> <commit>
  ```
- **Diff one file since a commit**
  ```bash
  git diff <commit> <file>
  ```
- **Show a summary of a diff**
  ```bash
  git diff <commit> --stat
  # OR
  git show <commit> --stat
  ```

### Ways to refer to a commit

Every time `<commit>` is used, you can use any of these:

| Reference Type  | Example               | Meaning                                                      |
| :-------------- | :-------------------- | :----------------------------------------------------------- |
| A branch        | `main`                | The tip of the `main` branch.                                |
| A tag           | `v0.1`                | The commit tagged as `v0.1`.                                 |
| A commit ID     | `3e887ab`             | A specific commit hash (usually shortened).                  |
| A remote branch | `origin/main`         | The state of the `main` branch on the remote named `origin`. |
| Current commit  | `HEAD`                | The commit you are currently on.                             |
| Past commits    | `HEAD^^^` or `HEAD~3` | The commit three steps before the current one.               |

---

## Code Archaeology

Commands for inspecting the repository's history.

- **Look at a branch's history**
  ```bash
  git log main
  ```
- **Show history with a graph**
  ```bash
  git log --graph main
  ```
- **Show history in a condensed format**
  ```bash
  git log --oneline
  ```
- **Show every commit that modified a file**
  ```bash
  git log <file>
  ```
- **Show every commit that modified a file, including before it was renamed**
  ```bash
  git log --follow <file>
  ```
- **Find every commit that added or removed some text**
  ```bash
  git log -G banana
  ```
- **Show who last changed each line of a file**
  ```bash
  git blame <file>
  ```
- **Restore an Old File**
  ```bash
  git checkout <commit> <file>
  # OR
  git restore <file> --source <commit>
  ```

---

## Edit History

Commands for rewriting or modifying past commits.

- **"Undo" the most recent commit (keep your working directory the same)**
  ```bash
  git reset HEAD^
  ```
- **Change a commit message (or add a file you forgot)**
  ```bash
  git commit --amend
  ```
- **Squash the last 5 commits into one**
  ```bash
  git rebase -i HEAD~6
  # Then change "pick" to "fixup" for any commit you want to combine with the previous one.
  ```
- **Undo a failed rebase**
  ```bash
  git reflog BRANCHNAME
  # Then manually find the right commit ID in the reflog, then run:
  git reset --hard <commit>
  ```

---

## Add a Remote

- **Connect a local repository to a remote one**
  ```bash
  git remote add <name> <url>
  ```

---

Since you use **NeoVim (Astronvim)**, you likely use a plugin like **Fugitive** or the built-in language server capabilities for Git integration. Having this Markdown cheat sheet nearby will make it even faster to look up a command\!

Would you like me to find some useful **Git aliases** that you could set up globally on your Linux system to make these commands even shorter?
