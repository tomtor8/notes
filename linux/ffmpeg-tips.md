## Music files metadata editing

The following example adds metadata to a song (the input and output file cannot be the same):

```bash
ffmpeg -i input.mp3 \
    -metadata artist="Sepultura" \
    -metadata album_artist="Sepultura" \
    -metadata album="Roots" \
    -metadata date="1996" \
    -metadata genre="Groove Metal" \
    -c copy output.mp3
```

Using a temporary file to create a file with new metadata and then on success moving the temporary file back to the original:

```bash
ffmpeg -i input.mp3 \
    -metadata artist="Sepultura" \
    -metadata album_artist="Sepultura" \
    -metadata album="Roots" \
    -metadata date="1996" \
    -metadata genre="Groove Metal" \
    -c copy temp.mp3 && mv temp.mp3 song.mp3
```

Embedding album art:

```bash
ffmpeg -i "01 Roots Bloody Roots.mp3" -i cover.jpg \
    -map 0:0 -map 1:0 -c copy \
    -metadata album="Roots" \
    -metadata artist="Sepultura" \
    output.mp3
```

```bash
ffmpeg -i song.mp3 -i cover.jpg \
    -map 0:0 -map 1:0 \
    -c copy \
    -id3v2_version 3 \
    -metadata:s:v title="Album cover" \
    -metadata:s:v comment="Cover (front)" \
    output.mp3
```

## Shot the song metadata info

The `ffprobe` tool is part of the `ffmpeg` suite.

```bash
 ffprobe -hide_banner -show_entries format_tags "some_song.mp3"
```

To get a nice formatted output of the most important tags:

```bash
ffprobe -v quiet -sexagesimal -show_entries format_tags=title,date,album,artist,track:format=duration -of default=noprint_wrappers=1 "some_song.mp3"
```
