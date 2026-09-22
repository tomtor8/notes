- to get the total download size in megabytes during the session:

```bash
ifconfig wlo1 | grep 'RX.*bytes' | sed -n 's/.*(\([^)]*\)).*/\1/p'
```

- to get the total upload size in megabytes during the session:

```bash
ifconfig wlo1 | grep 'TX.*bytes' | sed -n 's/.*(\([^)]*\)).*/\1/p'
```