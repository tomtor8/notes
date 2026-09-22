# Intro

I'm using on Arch DankMaterialShell Greeter as a login display manager.


## Installation

- on Arch install it using `paru`

- then `dms greeter enable`



```bash
sudo systemctl start greetd
sudo systemctl enable --now greetd
```

## Configuration

Manual enabling in the `/etc/greetd/config.toml`


```toml
[terminal]
# The VT to run the greeter on. Can be "next", "current" or a number
# designating the VT.
vt = 1

# The default session, also known as the greeter.
[default_session]

# `agreety` is the bundled agetty/login-lookalike. You can replace `/bin/sh`
# with whatever you want started, such as `sway`.

# The user to run the command as. The privileges this user must have depends
# on the greeter. A graphical greeter may for example require the user to be
# in the `video` group.
user = "greeter"
command = "/usr/bin/dms-greeter --command niri"
```
