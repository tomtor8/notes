# Check if a package is a dependency of another package

- useful if you want to remove some packages and you are not sure if the package is not an important dependency for another package

```bash
dnf repoquery --installed --whatrequires package_name
```
