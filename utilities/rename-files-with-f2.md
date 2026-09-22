## Rename files with F2

[https://f2.freshman.tech/](https://f2.freshman.tech/)

On Fedora installed via npm package manager:

`npm i @ayoisaiah/f2 -g`

## Important flags

`-f` means find

`-r` means replace

`-e` exclude extension from operations

`-x` execute the command

`-d` include directories

## Limiting the number of replacements

Replace limit with `-l` flag. The following will replace only the first matching occurrence.

```bash
f2 -f ' ' -r '_' -l 1
```

E.g. `01 Some great song.mp3` changes to `01_Some great song.mp3`

Replace limit range with `-L` flag. You can replace an occurrence in an arbitrary place.

```bash
f2 -f ' ' -r '_' -L '1;3'
```

E.g. `01 Some great song.mp3` changes to `01_Some great_song is here.mp3`

```bash
f2 -f ' ' -r '_' -L '1..3'
```

The following replaces only the second match:

```bash
f2 -f ' ' -r '_' -L '2'
```

E.g. `01 Some great song.mp3` changes to `01_Some_great_song is here.mp3`

## Make filenames lowercase

The following example works in the current directory and transforms all files

`-r` flag means replace

```bash
f2 -r '{f.lw}{ext}'
```

- uppercase works in the same vein using `.up` instead of `.lw` 

## Make filenames titlecase

```bash
f2 -r '{f.ti}{ext}'
```

## Make filenames AND extensions lowercase

The following makes all files in the directory lowercase including the extensions

```
f2 -r '{.lw}'
```

The following command makes lowercase all files beginning with BL

`f2 -r '{.lw}' BL*`

```text
_——————————_—————————_————————_
| ORIGINAL | RENAMED | STATUS |
_——————————_—————————_————————_
| BLA.txt | bla.txt | ok |
| BLO.txt | blo.txt | ok |
| BLU.txt | blu.txt | ok |
_——————————_—————————_————————_
```

## Replace spaces with - character recursively

Replace one or more space characters with one `-` character

```
f2 -f '\s+' -r '-' -R
```

The `-R` flag does recursive replacing in the children directories as well.

## Rename only directories with `-D` flag

```
f2 -f '(.*)' -r 'renamed-$1' -D
```

The above command prepends “renamed-” before the original name

## Number sequences

Prepend double digits, e.g. 01-filename, 02-filename…

```
f2 -f '(.*)' -r '{%02d}-$1' -e
```

`{5%d}` starting number is 5, using single digits, e.g. 5-filename, 6-filename…

The following command finds filenames NOT beginning with digit and prepends double digits beginning with 04.

```
f2 -f '^(\D.*)' -r '{4%02d}-$1' -e
```

```text
\*—————————————————\*————————————————————\*————————\*
|    ORIGINAL     |      RENAMED       | STATUS |
\*—————————————————\*————————————————————\*————————\*
| test-file-4.txt |      04-test-file-4.txt  |   ok     |
| test-file-5.txt |     05-test-file-5.txt  |   ok     |
| test-file-6.txt |     06-test-file-6.txt  |   ok     |
\*—————————————————\*————————————————————\*————————\*
```

## Remove prepended numbers

The following removes prepended two digits and - character

```
f2 -f '^\d{2}-' -r '' -e
```

## Replace spaces OR \_ with - characters

```
f2 -f '(_| )+' -r '-' -e
```

## Replace characters in specified files

The following command replaces all - with \_ characters in files containing `song` in their name.

`f2 -f '-' -r '_' *song*`

The same replacement in all files ending with `.md` extension.

`f2 -f '-' -r '_' *.md`

## Extract metadata from audio files to rename files

The following command will extract the title of the song from metadata and format the name like this `01_Some Song Name.mp3`

```bash
f2 -f '(\d+).*' -r '${1}_{id3.title}{ext}'
```

More on this topic at [f2 website](https://f2.freshman.tech/guide/id3-variables.html)
