# Regex patterns in Neovim

## Positive lookaheads

Find the word `is` that is followed by `,` in very magic mode.
The pattern is `@=`.

```vim
s/\vis(,)@=
```

## Negative lookaheads

Find the word `is` that is NOT followed by `,` in very magic mode.
The pattern is `@!`.

```vim
s/\vis(,)@!
```

## Positive lookbehind

Find the `,` character that is preceded by `is`.
The pattern is `@<=`.

```vim
s/\v(is)@<=,
```

## Negative lookbehind

Find the `,` character that is NOT preceded by `is`.
The pattern is `@<!`.

```vim
s/\v(is)@<!,
```
