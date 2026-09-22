# Create symlinks in git tracked directories

---

```sh
# Navigate to the directory
cd path/to/your/repo
# Create the relative symlink
ln -s markdown.json markdown_inline.json
# Add to git
git add markdown_inline.json
```

---

When creating symlink in tracked directories you should use relative paths. It's a good prevention of broken symlinks.
