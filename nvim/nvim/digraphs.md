# Digraphs

Entering special characters in vanilla Neovim using **digraphs** is a fantastic way to keep your workflow fast without relying on external plugins or snippet engines.

Here is a guide on how they work, how to use them, and some custom tricks for your setup.

## How to Use Digraphs

In **Insert mode** (or **Command-line mode**), you can enter a digraph using the following key combination:

`Ctrl + k` + **{char1}** + **{char2}**

Neovim will automatically combine the two characters into a single special character.

> **Tip:** The order of the two characters usually matters, but Neovim is smart. It often maps the most logical combination (e.g., a letter followed by a punctuation mark that looks like the accent).

### Common Examples

Here are some useful digraphs, focusing on characters you might need for Spanish, general formatting, or math:

| Character | Description               | Digraph Keys     | Visual Logic                           |
| --------- | ------------------------- | ---------------- | -------------------------------------- |
| **á**     | `a` with acute accent     | `Ctrl+k` `a` `'` | `a` + single quote                     |
| **ñ**     | `n` with tilde            | `Ctrl+k` `n` `~` | `n` + tilde                            |
| **¿**     | Inverted question mark    | `Ctrl+k` `?` `?` | Double question mark                   |
| **¡**     | Inverted exclamation mark | `Ctrl+k` `!` `!` | Double exclamation mark                |
| **—**     | Em-dash (long dash)       | `Ctrl+k` `m` `m` | Think of "m" for em-dash               |
| **λ**     | Greek lambda              | `Ctrl+k` `l` `*` | Letter `l` + asterisk (Greek modifier) |
| **→**     | Right arrow               | `Ctrl+k` `-` `>` | Looks like an arrow                    |

## How to Find Digraphs Inside Neovim

You don't need to memorize a massive list. Neovim has a built-in directory of all available digraphs.

- Run the command `:digraphs` (or `:dig`) to see the full list.
- If the list is too long, you can search for specific characters using standard Neovim search filtering, or view them in a scrollable window using:
  Vim Script
  ```
  :filter /pattern/ digraphs
  ```

## Custom Digraphs (No Plugins Required)

Since you prefer a vanilla Neovim configuration, you can easily define your own custom digraphs directly in your `init.lua`. This is incredibly useful if you find a specific sequence hard to remember or if you want to add custom symbols.

The syntax in Lua uses `vim.cmd` to call the Vim script `digraph` command:

```lua
-- Custom digraphs for your init.lua
-- Syntax: vim.cmd("digraph {char1}{char2} {number}")

-- Example: Define a custom shortcut for a checkbox
-- Using 'sh' (short for share/shrug or whatever you prefer) to output '¯\_(ツ)_/¯' isn't possible via digraphs
-- because digraphs only output a single UTF-8 character.
-- But you can map single custom characters like a checkmark:
vim.cmd("digraph OK 2713") -- 'O' + 'K' creates ✓ (Unicode 2713)
```

Here is how you can easily type Slovak and Spanish characters in Neovim using `Ctrl+k`. Both languages share some overlapping rules (like the acute accent), but they also feature distinct diacritics.

## Acute Accents / Dĺžne (`´`)

Both Spanish and Slovak use the acute accent (called _dĺžeň_ in Slovak). The logical rules in Neovim are very consistent: you type the **base letter**, followed by a **single quote (`'`)**.

| Target Character | Digraph Keys                        | Language         |
| ---------------- | ----------------------------------- | ---------------- |
| **á / Á**        | `Ctrl+k` `a` `'` / `Ctrl+k` `A` `'` | Spanish & Slovak |
| **é / É**        | `Ctrl+k` `e` `'` / `Ctrl+k` `E` `'` | Spanish & Slovak |
| **í / Í**        | `Ctrl+k` `i` `'` / `Ctrl+k` `I` `'` | Spanish & Slovak |
| **ó / Ó**        | `Ctrl+k` `o` `'` / `Ctrl+k` `O` `'` | Spanish & Slovak |
| **ú / Ú**        | `Ctrl+k` `u` `'` / `Ctrl+k` `U` `'` | Spanish & Slovak |
| **ý / Ý**        | `Ctrl+k` `y` `'` / `Ctrl+k` `Y` `'` | Slovak           |
| **ĺ / Ĺ**        | `Ctrl+k` `l` `'` / `Ctrl+k` `L` `'` | Slovak           |
| **ŕ / Ŕ**        | `Ctrl+k` `r` `'` / `Ctrl+k` `R` `'` | Slovak           |

## Carons / Mäkčene (`ˇ`)

Slovak relies heavily on the caron (_mäkčeň_). In Neovim, the caron modifier key is the **less-than sign (`<`)** because visually it looks like a small caron pointing left (`<`). Type the **base letter** followed by **`<`**.

| Target Character | Digraph Keys                        | Language |
| ---------------- | ----------------------------------- | -------- |
| **č / Č**        | `Ctrl+k` `c` `<` / `Ctrl+k` `C` `<` | Slovak   |
| **ď / Ď**        | `Ctrl+k` `d` `<` / `Ctrl+k` `D` `<` | Slovak   |
| **ľ / Ľ**        | `Ctrl+k` `l` `<` / `Ctrl+k` `L` `<` | Slovak   |
| **ň / Ň**        | `Ctrl+k` `n` `<` / `Ctrl+k` `N` `<` | Slovak   |
| **š / Š**        | `Ctrl+k` `s` `<` / `Ctrl+k` `S` `<` | Slovak   |
| **ť / Ť**        | `Ctrl+k` `t` `<` / `Ctrl+k` `T` `<` | Slovak   |
| **ž / Ž**        | `Ctrl+k` `z` `<` / `Ctrl+k` `Z` `<` | Slovak   |

## Umlauts / Diaereses (`¨`)

Both languages use the double-dot modifier—Slovak for the _vokáň_ variant overlay and Spanish for the _diéresis_ (as in _pingüino_). In Neovim, the modifier key is a **colon (`:`)**.

| Target Character | Digraph Keys                        | Language |
| ---------------- | ----------------------------------- | -------- |
| **ä / Ä**        | `Ctrl+k` `a` `:` / `Ctrl+k` `A` `:` | Slovak   |
| **ü / Ü**        | `Ctrl+k` `u` `:` / `Ctrl+k` `U` `:` | Spanish  |

## Circumflex / Vokáň (`ˆ`)

Slovak features the letter **ô**. The circumflex modifier in Neovim is the **greater-than sign (`>`)**, which visually acts like a roof pointing upward.

| Target Character | Digraph Keys                        | Language |
| ---------------- | ----------------------------------- | -------- |
| **ô / Ô**        | `Ctrl+k` `o` `>` / `Ctrl+k` `O` `>` | Slovak   |

## Unique Spanish Characters

Finally, these are the remaining Spanish-specific punctuation and character mappings from your guide:

| Target Character | Digraph Keys                        | Visual Logic              |
| ---------------- | ----------------------------------- | ------------------------- |
| **ñ / Ň**        | `Ctrl+k` `n` `~` / `Ctrl+k` `N` `~` | `n` + tilde               |
| **ñ / Ň**        | `Ctrl+k` `n` `?` / `Ctrl+k` `N` `?` | `n` + question mark       |
| **¿**            | `Ctrl+k` `?` `I`                    | Question mark Inverted    |
| **¡**            | `Ctrl+k` `!` `I`                    | Exclamation mark Inverted |

> **Pro-Tip:** If you ever forget uppercase variants, the logic remains the same. Capitalizing the first character of the digraph pair will automatically yield the uppercase accented character (e.g., `Ctrl+k` `Z` `<` produces **Ž**).
