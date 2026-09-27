# Variable dereferencing

**TL;DR:** In Fish, placing an extra `$` before a variable (e.g., `$$var`) treats the _value_ stored in `$var` as the _name_ of another variable to read from. It evaluates from the **inside out**: first Fish resolves `$var` into a name, then it resolves `$` + that name into the actual data.

## How Variable Dereferencing Works in Fish

Think of dereferencing as looking up an item by address rather than by name directly.

1.  Standard variable lookup (`$x`): "Look inside `$x` and give me what is stored there."
2.  Double dereference (`$$x`): "Look inside `$x`, treat that value as a variable name, and give me what _that second variable_ holds."

### Visualizing Double Dereference

Suppose you set up two variables:

```fish
set target "hello world"
set pointer "target"
```

Here is how Fish evaluates `$$pointer`:

```plaintext
$$pointer ──► Step 1: Evaluate inner variable ($pointer) ──► "target"
          ──► Step 2: Evaluate outer variable ($target)  ──► "hello world"
```

## Code Example: Dynamic Variable Access

```fish
# Store separate configuration lists
set production_servers "192.168.1.10" "192.168.1.11"
set staging_servers    "10.0.0.5"

# Dynamically choose which environment to query
set current_env production_servers

# $$current_env evaluates to $production_servers
echo $$current_env
# Output: 192.168.1.10 192.168.1.11
```

## How Slicing (Indexing) Works: Inside-Out Rule

When combining double-dollars `$$` with index brackets `[]`, Fish reads the brackets **from the inside out** (attaching the index to the inner variable first).

### Example 1: Indexing the Inner Variable

```fish
set listone 10 20 30
set listtwo 40 50 60
set var listone listtwo

# Evaluation order:
# 1. $var[2]       --> "listtwo"
# 2. $$var[2]      --> $listtwo
# 3. $listtwo[3]   --> 60
echo $$var[2][3]
# Output: 60
```

### Example 2: Indexing the Outer Variables

To index the resulting values _after_ dereferencing everything inside `$var`, use ranges `[..]` first:

```fish
# Evaluation order:
# 1. $var[..]      --> "listone" "listtwo"
# 2. $$var[..]     --> evaluates $listone and $listtwo (giving: 10 20 30 40 50 60)
# 3. ...[2]        --> extracts the 2nd element from each list (20 and 50)
echo $$var[..][2]
# Output: 20 50
```

## Lua & Fish Tips for Scripting

Here is how dereferencing compares between both languages:

### Lua Tip: Dynamic Variable Access

In Lua, variable scope determines how you dereference variable names dynamically. Global variables are stored in the `_G` table, allowing dynamic lookups similar to Fish's `$$`:

```lua
-- Lua equivalent to dynamic variable reading via global environment table
local target = "hello from Lua"
var_name = "target" -- must be global or in a table

print(_G[var_name]) -- Output: hello from Lua
```

### Fish Scripting Tip: Avoiding Variable Clashes

When passing variable names to functions in Fish for dereferencing, use local variables starting with `_` inside your function to prevent accidental name collisions:

```fish
function inspect_var
    # Use localized names prefixed with _ to avoid clashing with passed variable names
    for _arg in $argv
        echo "Value of $_arg is: $$_arg"
    end
end
```

Of course the variable will have to be accessible from the function, so it needs to be global/universal or exported. It also can’t clash with a variable name used inside the function. So if we had made $foo there a local variable, or if we had named it “arg” instead, it would not have worked. For more examples see the [fish documentation](https://fishshell.com/docs/current/language.html#dereferencing-variables).
