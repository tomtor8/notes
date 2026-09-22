# ⚡ Create a branch and switch to it immediately

While `git checkout -b` works perfectly, Git introduced a newer, more intuitive command for switching branches: `git switch`. This command is recommended for general branch switching because it separates the concepts of switching/creating branches (done by switch) from restoring files (done by restore).

To achieve the same result (create and switch) using the modern command:

```bash
git switch -c <new-branch-name>
```

`git switch`: The command for switching branches in general.

`-c`: This flag means -> create a new branch and switch to it.
