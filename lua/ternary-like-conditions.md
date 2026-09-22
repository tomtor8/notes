# Ternary-like conditions in Lua

## The Basic Syntax

The pattern looks like this:

```lua
local result = (condition) and (value_if_true) or (value_if_false)

```

### How it works logically:

Lua uses **short-circuit evaluation**:

1. **`and`**: If the left side is "truthy," it moves to the right side. If the left side is "falsy" (`nil` or `false`), it stops and returns the left side.
2. **`or`**: If the left side is "truthy," it stops and returns the left side. If the left side is "falsy," it moves to the right side.

---

## Applying it to your Bitrate Example

Let's look at the line I suggested earlier:

```lua
local bitrate = raw_bitrate and (math.floor(raw_bitrate / 1000 + 0.5) .. " kb/s") or "unknown"

```

1. **Case A: `raw_bitrate` exists (e.g., "320000")**

- `raw_bitrate` is "truthy."
- Because of the `and`, Lua moves to the next part: `(math.floor(...) .. " kb/s")`.
- This result is also "truthy."
- Because of the `or`, and because the left side is now "truthy," Lua stops and ignores `"unknown"`.
- **Result:** `"320 kb/s"`

2. **Case B: `raw_bitrate` is `nil**`

- `raw_bitrate` is "falsy."
- Because of the `and`, the entire first section becomes `nil`.
- Now we have `nil or "unknown"`.
- Because the left side of `or` is "falsy," Lua moves to the right side.
- **Result:** `"unknown"`

---

## The "Danger Zone" (Important!)

There is one specific scenario where this syntax **fails**. For this to work as a true ternary, the **middle value** (the `value_if_true`) **must not be `nil` or `false**`.

If your "true" value is `false`, Lua will skip over it and return the "else" value instead.

| Condition | True Value  | False Value | Result      | Status     |
| --------- | ----------- | ----------- | ----------- | ---------- |
| `true`    | `"Hello"`   | `"Bye"`     | `"Hello"`   | **Works**  |
| `false`   | `"Hello"`   | `"Bye"`     | `"Bye"`     | **Works**  |
| `true`    | **`false`** | `"Bye"`     | **`"Bye"`** | **Fails!** |

> **Neovim Tip:** In your **AstroNvim** configuration, you will see this used constantly for setting options. For example:
> `local icon = has_nerd_fonts and " " or "Time:"`
