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
