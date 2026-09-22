# Install gnome-keyring

- important for clipboard history to work

```bash
sudo pacman -S gnome-keyring
```

# Creating a Secret Key File if you didn't install gnome keyring

The `install` command creates a directory with 700 permissions. Or change the existing directory permissions with `chmod`.

```bash
install -dm 700 ~/.config/noctalia
umask 077
head -c 32 /dev/urandom | hexdump -v -e '1/1 "%02x"' > ~/.config/noctalia/noctalia-storage-key
```

The resulting noctalia-storage-key should have 600 permissions i.e. `.rw-------`

Put this to `~/.config/noctalia/config.toml`

```toml
[storage]
key_source = "file"
key_file = "/home/tom/.config/noctalia/noctalia-storage-key"
```
