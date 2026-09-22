# How to check the operating system

A quick way to check if you're running Fedora or CachyOS in a bash script is to examine the contents of the `/etc/os-release` file. This file contains a number of variables, including `ID` and `PRETTY_NAME`, which can be used to identify the operating system.

```bash
#!/bin/bash

if grep -q "ID=fedora" /etc/os-release; then
  echo "You are running Fedora."
elif grep -q "ID=cachyos" /etc/os-release; then
  echo "You are running CachyOS."
else
  echo "You are running a different operating system."
fi
```

The `-q` flag of the `grep` command stands for "quiet" and suppresses any output, so it only returns an exit status (0 for success, 1 for failure). This makes it ideal for use in an `if` statement.
