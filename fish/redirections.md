## Redirect errors to a custom file

```sh
echo "This goes to errorfile.txt" 2>errorfile.txt >&2
```

## Redirecting stdout (1) and stderr (2) to the same file

```sh
# the `&>` shortcut
echo hello &> all_output.txt
# the following does the same
echo hello > all_output.txt 2>&1
```

In the previous longer version of the command the `echo hello` stdout is redirected to a file `all_output.txt` and then the stderr is redirected to the stdout (already redirected to a file).

## Redirecting blocks of commands

```sh
begin
    echo "First line that goes to testoutput.txt"
    echo "Second line that goes to testoutput.txt"
    echo "This goes to errorfile.txt" 2>errorfile.txt >&2
end >testoutput.txt
```

The contents of the `testoutput.txt`:

```text
$ cat testoutput.txt
First line that goes to testoutput.txt
Second line that goes to testoutput.txt
```

The contents of the `errorfile.txt`:

```text
This line goes to errorfile.txt.
```
