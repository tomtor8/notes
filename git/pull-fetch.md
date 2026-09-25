# Fetch

## Fetch from the default remote

- run `git fetch -p`
- `-p` stands for prune

## See what changed

```bash
git log main..origin/main --oneline
```

## Merge if everything is ok

```bash
git merge origin/main
```

---

# Pull

- you can use `git pull` instead of the above if you are sure there are no conflicts
- the `fetch` way of doing things is much safer

# Rebase

## Commiting local changes before pulling from remote

When you commit local changes before pulling, your local branch and remote branch diverge. Using `--rebase` is the cleanest way to handle this without creating unnecessary merge commits.

```bash
git pull --rebase origin main
```

### What Happens Next:

- **If there are no overlapping edits:** Git fetches the remote commits, rewinds your local commit, applies the remote updates, and then replays your commit on top. Everything is clean, linear, and ready for `git push`.
- **If there are conflicts:** Git will stop and highlight the conflicting files.
  1. Open the conflicted files in Neovim and resolve the conflicts.
  2. Stage the resolved files: `git add <file>`
  3. Continue the rebase: `git rebase --continue` _(If you ever want to abandon the process and go back to where you started, run `git rebase --abort`)._
