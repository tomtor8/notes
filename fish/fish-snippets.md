## Test file

`file.txt` contains four lines of text separated by newlines and a trailing newline

```text
First line
Second line
Third line

```

## Read lines of a file line by line

- read lines from `file.txt`
- store the lines in the variable `line`
- print the lines
- redirect the contents of the `file.txt` to the `while` loop

```sh
while read -l line
  echo line: $line
end <file.txt
```

**Output:**

```text
line: First line
line: Second line
line: Third line
```

## Get text from command substitutions

```sh
set text (cat file.txt)
echo $text
```

- the command substitution gets the four lines as individual four list items
- the newline characters are removed

**Output:**

```text
First line Second line Third line
```

### Keep the individual lines with newlines

```sh
set text (cat file.txt | string collect)
echo $text
```

**Output:** The last trailing newlines is trimmed automatically.

```text
First line
Second line
Third line
```

- the `string collect` trims the LAST newline character of the string, or block
- see the following example
- if the `file.txt` contains a trailing newline

```sh
set text (cat file.txt | string collect)
echo $text Another text
```

**Output:**

```text
First line
Second line
Third line Another text
```

- if you don't want to trim the last trailing newline, use the `-N` or `--no-trim-newlines` option

```sh
set text (cat file.txt | string collect -N)
echo $text Another text
```

**Output:**

```text
First line
Second line
Third line

 Another text
```

## Brace expansion

Study the following expansions, the first variation contains a comma after the last option in the braces.

**Example with ending comma in braces:**

```sh
set text {/usr,/var,/log,}/bin
echo $text
```

```text
/usr/bin /var/bin /log/bin /bin
```

**Example without ending comma in braces:**

```sh
set text {/usr,/var,/log}/bin
echo $text
```

```text
/usr/bin /var/bin /log/bin
```

## Wildcards, globbing

- the following script shows all complete filepaths of all mp3 and m4a files in a specified directory
- in this case the slipknot directory contained 2 subdirectories with 27 m4a and 1 mp3 file
- the `**.{m4a,mp3}` searches m4a and mp3 files RECURSIVELY

```sh
set musicdir /home/tom/Music/slipknot
set musicfiles $musicdir/**.{m4a,mp3}
set countoffiles (count $musicfiles)
# show all files / complete paths
echo "$musicfiles"
echo There are $countoffiles audio files in $musicdir.
```

## Math operations

- use `math` command
- to add up all mp3, mp4 files and then print their sum:

```sh
set musicdir /home/tom/Music/slipknot
set mp3s $musicdir/**.mp3
set m4as $musicdir/**.m4a
set countofmp3s (count $mp3s)
set countofm4as (count $m4as)
set totalaudiofiles (math $countofmp3s + $countofm4as)
echo There are $countofmp3s mp3 files, $countofm4as m4a files, total is $totalaudiofiles
```

**Output:**

```text
There are 1 mp3 files, 27 m4a files, total is 28
```
