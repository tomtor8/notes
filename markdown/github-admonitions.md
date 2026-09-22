# GitHub blockquote admonitions

These are an extension of the standard Markdown blockquote syntax.

---

## Using GitHub Admonitions (Callout Blocks)

To create these colored alert blocks, you still use the blockquote marker (`>`), but you must immediately follow it with a specific **keyword** in uppercase, enclosed in square brackets: `[!KEYWORD]`.

### 1\. Basic Admonition Structure

The structure for an admonition is:

```markdown
> [!KEYWORD]
> Content of the alert goes here.
```

- The keyword must be on its own line immediately after the `>` marker.
- You must leave a blank line (`>`) between the keyword and the content for proper rendering.

### 2\. Available Admonition Types

There are several keywords you can use, each providing a different color and icon for the alert.

| Keyword     | Use Case                                            | Resulting Alert Type       |
| :---------- | :-------------------------------------------------- | :------------------------- |
| `NOTE`      | General information or helpful tips.                | Blue/Purple                |
| `TIP`       | Advice for a better or faster workflow.             | Cyan                       |
| `IMPORTANT` | Crucial information that must be considered.        | Blue/Purple (same as NOTE) |
| `WARNING`   | Something that may cause minor issues.              | Orange                     |
| `CAUTION`   | Something that can cause major issues or data loss. | Yellow                     |

### 3\. Examples

Here are the specific examples for the types you mentioned:

#### **NOTE / IMPORTANT (Blue Block)**

**Markdown Syntax:**

```markdown
> [!NOTE]
> Remember to **reboot your system** after installing the new drivers.
```

**Result (What it looks like on GitHub):**

> [!NOTE]
> Remember to **reboot your system** after installing the new drivers.

#### **WARNING (Orange Block)**

**Markdown Syntax:**

```markdown
> [!WARNING]
> This feature is deprecated and will be removed in the next major release.
```

**Result:**

> [!WARNING]
> This feature is deprecated and will be removed in the next major release.
