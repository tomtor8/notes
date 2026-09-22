## Shell script to set random wallpaper

- create a shell script to randomly pick a wallpaper from a directory of wallpapers, in this case `set-rand-wallpaper.sh`
- you have to have the `hyperpaper` application installed

```bash
#!/bin/bash

## HOW TO USE IT IN HYPRLAND CONFIG FILE
# set default wallpaper in hyprpaper.conf
## set random wallpaper on startup - this does not work for me
# exec-once=/path/to/set_random_wallpaper.sh # do not do it
## set keybinding to change wallpaper manually - it works
# bind=SUPER, W, exec,/path/to/set_random_wallpaper.sh
## change wallpaper every hour using cron table `crontab -e`
# 0 * * * * /path/to/set_random_wallpaper.sh
# or better use systemd timer - see user guide in hypr config dir

# Set your wallpaper directory
WALLPAPER_DIR="/home/tom/Pictures/wallpapers/watercolor/"

# Find all wallpaper files and select one randomly
wallpaper=$(find "$WALLPAPER_DIR" -type f | shuf -n 1)

# Get the list of monitors
monitors=$(hyprctl monitors | grep "Monitor" | awk '{print $2}')

# Preload the selected wallpaper
hyprctl hyprpaper preload "$wallpaper"

# Set the wallpaper for each monitor
for monitor in $monitors; do
  hyprctl hyprpaper wallpaper "$monitor,$wallpaper"
done

```

## Create a systemd timer to run the script

- schedule to run the above script every hour on the hour
- create a timer file `/home/tom/.config/systemd/user/set-rand-wallpaper.timer`

```toml
[Unit]
Description=Schedule a wallpaper every 1 hour
RefuseManualStart=no  # Allow manual starts
RefuseManualStop=no   # Allow manual stops

[Timer]
Persistent=true
# run every hour on hour
OnCalendar=*-*-* *:00:00
# File describing job to execute
Unit=set-rand-wallpaper.service

[Install]
WantedBy=timers.target
```

- create a service file `/home/tom/.config/systemd/user/set-rand-wallpaper.service`

```toml
[Unit]
Description=A job to change wallpapers in hyprland every hour

[Service]
Type=simple
ExecStart=/home/tom/Code/scripts/set_rand_wallpaper_hyprland/set-rand-wallpaper.sh

[Install]
WantedBy=default.target
```

- then enable and start the service as described in the article [[../systemd-timer-for-scheduling-tasks.pdf|systemd-timer-for-scheduling-tasks]]
