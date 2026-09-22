# Markdown Syntax

## Editor settings for trailing spaces

- two trailing spaces are important for creating continuous blocks of text
- in your text editors turn off automatic deletion of trailing white spaces for Markdown

In **Zed editor**:

```json
"languages" : {
    "Markdown": {
      "show_whitespaces": "boundary",
      "format_on_save": "on",
      "remove_trailing_whitespace_on_save": false
    },
}
```

In **Neovim**:

```lua
-- Enable list mode to show the characters defined in 'listchars'
vim.opt.list = true
-- Define the characters for various whitespace types
vim.opt.listchars = {
  tab = '▸ ',   -- Tabs are shown as '▸' followed by a space
  trail = '·',  -- Trailing spaces are shown as '·'
  nbsp = '␣',   -- Non-breaking space
  eol = '↵',    -- End of Line (for non-Unix files, like DOS/Windows)
```

> [!CAUTION]
> In the lsp or formatter settings don't allow autoremove of the trailing white spaces.

## Two trailing spaces usage

### Paragraphs

- to create a paragraph of text with newlines, use two white spaces at the end of the line
- after the last sentence of the paragraph, just press Enter twice and you are in the next paragraph

This is my normal text.  
This is another line  
First is here.

Another paragraph.  
Second line ges here

### Blockquotes

- the following quote is seemingly separated by newlines and shown as a single quote
- the first and second line of the quote is ended by two spaces
- if there were no spaces at the end, the quote would end up on a single line
- the last line doesn't need trailing spaces
- after the quote press Enter twice and start typing another paragraph of text

### More lines of a single paragraph in a quote

> Note is here  
> Another line of quote.  
> This belongs here

### More paragraphs in a single quote

> First line of another quote  
> Second line of the quote.
>
> Another paragraph within the quote  
> Second line of the second paragraph

### Nested quote

> First line of the outer quote
>
> > Nested quote first line  
> > Second line of the nested quote
>
> Outer quote confinues

## Newlines

- newlines between list items (beginning with `-` or `*`) have no effect
- every block of a document (paragraphs, lists, quotes ...) must be separated by an empty line

## Indented lists

- use tabs to create indented lists

```markdown
- first list
- second list item
  - indented list item
```

- first list
- second list item
  - indented list item

## Tables

```markdown
| header1    | header2     | header3    |
| ---------- | ----------- | ---------- |
| First item | Second item | Third item |
```

| header1    | header2     | header3    |
| ---------- | ----------- | ---------- |
| First item | Second item | Third item |

## Superscript

- superscripts don't work in every editor
- on Github we try out the sub and sup tags in the following test:

The formula of water: H<sub>2</sub>O

Squaring: 3<sup>2</sup> = 9

## Footnotes (GitHub)

> [!NOTE]
> It doesn't matter where you put the footnote texts, they always end up being at the bottom of the document.

This sentence ends with a footnote. [^1]

[^1]: This is the footnote text

Second sentence after the footnote.

Another sentence ends with a second footnote. [^2]

Third sentence is here.

[^2]: This is the second footnote text.
