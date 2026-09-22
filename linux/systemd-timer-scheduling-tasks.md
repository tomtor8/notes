# ⏱️ Systemd Timer for Scheduling Tasks

## ⚙️ Configuration

Systemd timer-executed functions require, at a minimum, two files: the **"timer unit"** and the **"service unit"**.

- The **timer unit file** defines the schedule. (More details are available in `man systemd.timer`).
- The **service unit file** defines the task(s) performed. (Details are available in `man systemd.service`).
- If the action consists of more than a simple command, you will also need a **"job" file or script** to perform the necessary functions.

### 📂 Unit File Location

Unit files can exist in several locations. The easiest location for a general user is typically `~/.config/systemd/user`. This setup creates a **user scheduled job** rather than a system scheduled job (which would run as root).

---

## 🚀 Demo: Simple Scheduled Job

This demo creates a job that prints a message, date, and time to a file.

### 1\. Create the Shell Script (Job File)

Create a shell script in your local `bin` directory, for example, in `~/bin/schedule-test.sh`.

```bash
touch ~/bin/schedule-test.sh
```

Add the following content to the script:

```bash
#!/bin/sh
echo "This is only a test: $(date)" >> "$HOME/schedule-test-output.txt"
```

**Remember to make your shell script executable.**

### 2\. Create the Service Unit (`.service` file)

This file calls the script above. Create the file at: `~/.config/systemd/user/schedule-test.service`.

```ini
[Unit]
Description=A job to test the systemd scheduler

[Service]
Type=simple
ExecStart=/home/<user>/bin/schedule-test.sh

[Install]
WantedBy=default.target
```

> **Note on `ExecStart`:** The `ExecStart` line should use an absolute address. For user units, you may substitute **`%h`** for `$HOME`.
>
> **Recommended:**
> `ExecStart=%h/bin/schedule-test.sh`

### 3\. Create the Timer Unit (`.timer` file)

This file schedules the service unit. Create it in the same location as the service file: `~/.config/systemd/user/schedule-test.timer`. The file names differ only in their extensions (`.service` vs. `.timer`).

```ini
[Unit]
Description=Schedule a message every 1 minute
RefuseManualStart=no # Allow manual starts
RefuseManualStop=no # Allow manual stops

[Timer]
# Execute job if it missed a run due to machine being off
Persistent=true
# Run 120 seconds after boot for the first time
OnBootSec=120
# Run every 1 minute thereafter
OnUnitActiveSec=60
# File describing job to execute
Unit=schedule-test.service

[Install]
WantedBy=timers.target
```

#### 📅 Scheduling Options

While the demo used `OnUnitActiveSec`, the more flexible option is **`OnCalendar`**.

| Schedule Example                                         | `OnCalendar` Value |
| :------------------------------------------------------- | :----------------- |
| Run on the minute of every minute, every hour, every day | `*-*-**:*:00`      |
| Run on the hour of every hour, every day                 | `*-*-**:00:00`     |
| Run every day at midnight                                | `*-*-* 00:00:00`   |

### 4\. Test the Job (Service)

First, **enable the user service**:

```bash
$ systemctl --user enable schedule-test.service
```

Next, do a **test run of the job**:

```bash
$ systemctl --user start schedule-test.service
```

Check your output file (`$HOME/schedule-test-output.txt`) to ensure the script is performing correctly.

### 5\. Schedule the Job (Timer)

Once the job works correctly, schedule it by **enabling and starting the user timer**:

```bash
$ systemctl --user enable schedule-test.timer
$ systemctl --user start schedule-test.timer
```

---

## 🖥️ Other Operations

### Monitoring

Check and monitor the service status (especially useful if you receive an error):

```bash
$ systemctl --user status schedule-test
$ systemctl --user list-unit-files
```

### Stop/Disable

To manually stop the service:

```bash
$ systemctl --user stop schedule-test.service
```

To permanently stop and disable everything, reload the daemon config, and reset failure notifications:

```bash
$ systemctl --user stop schedule-test.timer
$ systemctl --user disable schedule-test.timer
$ systemctl --user stop schedule-test.service
$ systemctl --user disable schedule-test.service
$ systemctl --user daemon-reload
$ systemctl --user reset-failed
```

---

## 🖼️ Example: Hyprland Wallpaper Scheduler

Here is an example of a timer unit for scheduling wallpapers in **Hyprland**:

### `set-rand-wallpaper.timer`

```ini
[Unit]
Description=Schedule a wallpaper every 1 hour
RefuseManualStart=no # Allow manual starts
RefuseManualStop=no # Allow manual stops

[Timer]
Persistent=true
# run every hour on hour
OnCalendar=*-*-**:00:00
# File describing job to execute
Unit=set-rand-wallpaper.service

[Install]
WantedBy=timers.target
```

### `set-rand-wallpaper.service`

```ini
[Unit]
Description=A job to change wallpapers in hyprland every hour

[Service]
Type=simple
ExecStart=/home/tom/Code/scripts/set_rand_wallpaper_hyprland/set

[Install]
WantedBy=default.target
```
