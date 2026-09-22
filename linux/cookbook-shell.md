## List last boot and poweroff times

`journalctl --list-boots`

## Get the date and time of the last logout

- `grep` gets the index `-1`, the last but one entry
- `awk` prints out the 7th, 8th and 9th column

```sh
journalctl --list-boots | grep -e "^ *-1 " | awk '{print "last logout on " $7 ", " $8 " at " $9}'
```

## Get the date of the last system update:

- pacman log entries are important, don't clean that file

```sh
grep "starting full system upgrade" /var/log/pacman.log | tail -n 1
```

### Print out the formatted date and time of the last system update

```sh
grep "starting full system upgrade" /var/log/pacman.log | tail -n 1 | awk -F'[[T]' '{print "last system update on " $2 " at " substr($3, 1, 8)}'
```

## `mpv` player minimal output for audio

```sh
mpv --no-video --term-osd-bar=yes --term-osd-bar-chars="|--|" --msg-level=all=status,ao=no,cplayer=warn <file-path>
```

## Display date

```sh
date +"%a - %b %d - %H:%M:%S"
```

**Output:** Sun - Apr 12 - 10:07:37
