## yt-dlp tool

The following example downloads all the videos in a YouTube Music playlist (album) and saves them as mp3 files.
The `--audio-quality 0` means the best quality.

```bash
yt-dlp -x --audio-format mp3 --audio-quality 0 -o "%(playlist_index)s - %(title)s.%(ext)s" "https://music.youtube.com/playlist?list=OLAK5uy_kEuUw9XSVYzmJtGxgbaxjelsln3tyzsJ8"
```

The following example downloads a track from Bandcamp.com:

```bash
yt-dlp -x --audio-format mp3 --audio-quality 0 "https://centurymedia.bandcamp.com/track/the-alchemist"
```
