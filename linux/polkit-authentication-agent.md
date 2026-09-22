# Verification

To verify that your polkit (PolicyKit) agent is running and functioning properly, you can test it in three main steps: check the active process, inspect system logs, and trigger a privilege escalation prompt.

## Check if the agent process is running

Run `pgrep` or `ps` to see if your desktop environment's authentication agent is actively running in the background:

```bash
# Check common polkit agents
pgrep -fl "polkit-.*-authentication-agent"
```

Common agent binary names include:

- **polkit-gnome-authentication-agent-1** (GNOME)
- **polkit-kde-authentication-agent-1** (KDE Plasma)
- **polkit-mate-authentication-agent-1** (MATE)
- **polkit-lxqt-agent** (LXQt)
- **hyprpolkitagent** or **polkit-gnome** (Wayland / tiling compositors)

## Trigger an authentication prompt

The most direct way to test the agent visually is to run an action that requires administrative privileges without using `sudo`. The `pkexec` utility invokes `polkit` directly:

```bash
pkexec echo "Polkit agent working!"
```

- **Working:** A graphical or terminal authentication window pops up asking for your user/root password, and executing the command outputs `"Polkit agent working!"`.
- **Not working:** You will see an error in the terminal:
  > `Error registered authentication agent: ...`
  >
  > or `Cannot run program: No authentication agent found.`

## Inspect system logs

If the prompt fails or hangs, check `journalctl` to view `polkitd` events and agent registrations in real time:

```bash
journalctl -u polkit.service -e --no-pager
```

Look for a log line confirming registration when the agent starts up:

```text
Registered Authentication Agent for unix-process:... (system bus name ...)
```

## Troubleshooting Tip

If no agent is running, you can manually test-launch your installed agent in the terminal to see debug output:

```bash
# Example for GNOME polkit agent on Arch/Debian derivatives:
/usr/lib/polkit-gnome/polkit-gnome-authentication-agent-1
```

_(Path varies by distro; check `/usr/lib/` or `/usr/libexec/` for your specific installed agent.)_
