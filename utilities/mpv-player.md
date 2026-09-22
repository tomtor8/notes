# Usage

- to play a song or an entire directory of songs without the GUI window

```bash
# audio file
mpv --no-video "song.mp3"
# dir
mpv --no-video "dirname"
# playing the entire dir while inside
mpv --no-video .
```

# Keybindings

- these keybindings work also in the terminal

`p` or `space` - play or pause
`q` - stop and quit
`Q` - stop and quit but remember the last position in the song
`[` - decrease the playback speed
`]` - increase the playback speed
`enter` - play the next song in the playlist (if playing the whole directory)
`>` - play the next song
`<` - play the previous song
`left arrow` - jump back 5s
`right arrow` - jump forward 5s
`up arrow` - jump forward 1 min
`down arrow` - jump back 1 min
`g p` - choose a song from the playlist
`l` - set point A and B in a loop and then remove the loop (the third press of l clears the loop)
`L` - repeat a single file infinitely, second press removes the repeat
