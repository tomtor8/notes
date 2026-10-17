# Logging in Fish

## Custom fish function located at `~/.config/fish/functions`

```fish
function log_msg --argument-names level message
    set -l timestamp (date "+%Y-%m%d %H:%M:%S")
    set -l logfile "$HOME/.cache/find_music/music-logs.log"
    set -l formatted_msg "[$timestamp] ["(string upper $level)"] $message"

    switch $level
        case error
            echo $formatted_msg | tee -a $logfile >&2
        case '*'
            echo $formatted_msg | tee -a $logfile
    end
end
```

## USAGE

- levels -> INFO, DEBUG, WARN, ERROR or anything else you want

```fish
log_msg error "Failed to connect to the database."
```

- you can use the function in any of your scripts on a local machine
- otherwise in the function is not located in the `fish/functions` directory you have to import or source the function into a script

```fish
source ~/path/to/function.fish
```

## Using logging after checking `$status`

- in Fish, `$status` holds the return code of the last executed command (`0` means success, non-zero indicates an error) 
- you can log errors immediately after critical operations:

```fish
#!/usr/bin/env fish

log_msg info "Starting system update..."

# Attempt a critical command
sudo pacman -Syu --noconfirm
if test $status -ne 0
    log_msg error "System update failed with status code $status."
    exit 1
end

log_msg info "System update completed successfully."
```

## Using with `if-then`

```fish
local mydir /non/existent/directory
if test -d "$mydir"
    log_msg debug "Directory $dir exists"
else
    log_msg warn "Directory $dir doesn't exist."
```

