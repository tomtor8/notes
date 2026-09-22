# Ternary operators

## Ternary a and b or c

In Lua, the idiom `a and b or c` is widely used as a substitute for the ternary operator (`a ? b : c` in C/C++/Java) because of how Lua evaluates logical operators.

### How Lua Evaluates `and` / `or`

Unlike languages where logical operators strictly return `true` or `false`, Lua's `and` and `or` operators return the **value of one of their operands**:

- `x and y`: Evaluates `x`. If `x` is `nil` or `false` (falsy), it short-circuits and returns `x`. Otherwise, it evaluates and returns `y`.  
- `x or y`: Evaluates `x`. If `x` is truthy (not `nil` and not `false`), it short-circuits and returns `x`. Otherwise, it evaluates and returns `y`.

### Step-by-Step Breakdown of `condition and val1 or val2`

The expression is evaluated from left to right as `(condition and val1) or val2`:

#### Case 1: `condition` is Truthy

1. `condition and val1` evaluates `condition`. Since it is truthy, the `and` operator returns **`val1`**.  
2. The expression simplifies to `val1 or val2`.  
3. If **`val1` is truthy**, the `or` operator short-circuits and returns **`val1`** (ignoring `val2`).

#### Case 2: `condition` is Falsy (`false` or `nil`)

1. `condition and val1` evaluates `condition`. Since it is falsy, the `and` operator short-circuits and returns **`condition`** (`false` or `nil`).  
2. The expression simplifies to `falsy_value or val2`.  
3. Since the left side of `or` is falsy, the `or` operator evaluates and returns **`val2`**.

### The Gotcha: When `val1` is `false` or `nil`

This idiom **breaks** if `val1` evaluates to `false` or `nil`.

If `condition` is `true`, but `val1` is `false`:

1. `true and false` returns `false`.  
2. `false or val2` returns **`val2`** (the wrong branch\!).

local condition \= true  
local val1 \= false  
local val2 \= "fallback"

\-- Intended: return false

\-- Actual: returns "fallback"

local result \= condition and val1 or val2

print(result) \-- "fallback"

### Modern Alternative (Lua 5.4+ / Safe Alternatives)

To avoid this edge case in Lua, you have two options:

#### 1\. Standard `if-else` (Always Safe)

local result

if condition then  
  result \= val1  
else  
  result \= val2  
end

#### 2\. The Parenthesized Table Trick

If `val1` might be `false` or `nil`, you can wrap values in single-element tables so the result is always a truthy table object:

local result \= (condition and { val1 } or { val2 })\[1\]

## Ternary a and b and c

### The Expression: `return b and b >= 48 and b <= 57`

In Lua, logical operators (`and` / `or`) evaluate using short-circuit logic and return the **value** of the operand that determined the result, not just `true` or `false`.

Here is how Lua evaluates `b and b >= 48 and b <= 57`:

1. **`b and ...`**: First, Lua evaluates `b`.  
     
   - If `b` is `nil` or `false`, the `and` chain short-circuits immediately and returns `b` (`nil` or `false`).  
   - If `b` is truthy (e.g., a number like `52`), Lua continues to the next operand.

   

2. **`... b >= 48`**: Evaluates the comparison operator `>=`.  
     
   - Comparisons in Lua **always evaluate to a boolean** (`true` or `false`).

   

3. **`... and b <= 57`**: Evaluates the final comparison operator `<=`.  
     
   - Again, this returns a boolean (`true` or `false`).  
- **Returns:** `true` or `false` (or `nil` if `b` was `nil`).

### Comparison of Outcomes

| Input (`b`) | `if b >= 48 and b <= 57 then return b end` | `return b and b >= 48 and b <= 57` |
| :---- | :---- | :---- |
| `52` (digit) | `52` (Number) | `true` (Boolean) |
| `65` (non-digit) | `nil` | `false` (Boolean) |
| `nil` | `nil` | `nil` |

### What It Is Actually a Shortcut For

The expression `return b and b >= 48 and b <= 57` is a shortcut for returning a **boolean check**:

\-- Long form boolean function

local function is\_digit(b)

  if b \~= nil and b \>= 48 and b \<= 57 then  
    return true  
  else  
    return false  
  end  
end

\-- Concise shortcut (equivalent behavior)

local function is\_digit(b)  
  return b and b \>= 48 and b \<= 57  
end  
