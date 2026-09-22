# edge-tts

## Installation

- python module
- install `pip install edge-tts`

[github link](https://github.com/rany2/edge-tts)

## List available voices

```bash
edge-tts --list-voices
edge-tts --list-voices | grep -E "Hu|Ru|Es|En"
```

## Usage

```bash
edge-tts --text "Hello, world!" --write-media hello.mp3 --write-subtitles hello.srt
```

### Using a specified voice

```bash
edge-tts --text "Hello, Linux enthusiast." --write-media output.mp3 --voice en-US-AriaNeural
```

### Changing rate, volume, pitch

```bash
edge-tts --rate=-50% --text "Hello, world!" --write-media hello_with_rate_lowered.mp3 --write-subtitles hello_with_rate_lowered.srt
edge-tts --volume=-50% --text "Hello, world!" --write-media hello_with_volume_lowered.mp3 --write-subtitles hello_with_volume_lowered.srt
edge-tts --pitch=-50Hz --text "Hello, world!" --write-media hello_with_pitch_lowered.mp3 --write-subtitles hello_with_pitch_lowered.srt
```
