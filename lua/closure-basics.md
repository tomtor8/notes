# Closures

A closure in Lua is a function that remembers and can access the variables from its enclosing lexical scope, even after that outer function has finished executing.

When a function is nested inside another, it creates a binding to the outer function's local variables (known as **upvalues**).

## How Closures Work

In Lua, functions are first-class values. When an inner function uses a local variable from an outer function, Lua retains that variable for the inner function's lifespan.

\-- Outer function acting as a factory

local function make\_counter(start\_value)

    \-- 'count' is an upvalue (captured local variable)

    local count \= start\_value or 0

    \-- Returns an anonymous inner function (closure)

    return function()

        count \= count \+ 1

        return count

    end

end

\-- Create two independent counters

local counter\_a \= make\_counter(10)

local counter\_b \= make\_counter(0)

print(counter\_a()) \-- Output: 11

print(counter\_a()) \-- Output: 12

print(counter\_b()) \-- Output: 1

print(counter\_a()) \-- Output: 13 (counter\_a keeps its own state)

## Practical Use Cases in Lua

### Data Encapsulation / Private State

Since Lua does not have built-in object-oriented access modifiers like `private`, closures are the primary way to hide state.

local function create\_bank\_account(initial\_balance)

    local balance \= initial\_balance \-- Hidden variable

    return {

        deposit \= function(amount)

            balance \= balance \+ amount

            return balance

        end,

        withdraw \= function(amount)

            if amount \<= balance then

                balance \= balance \- amount

                return balance

            else

                return nil, "Insufficient funds"

            end

        end,

        get\_balance \= function()

            return balance

        end

    }

end

local account \= create\_bank\_account(100)

account.deposit(50)

print(account.get\_balance()) \-- Output: 150

\-- 'balance' cannot be accessed directly from the outside\!

### Custom Iterators

Custom stateful iterators for `for` loops rely heavily on closures.

\-- Custom iterator that yields powers of two

local function powers\_of\_two(max\_exponent)

    local exp \= 0

    return function()

        if exp \<= max\_exponent then

            local val \= 2 ^ exp

            exp \= exp \+ 1

            return val

        end

    end

end

for val in powers\_of\_two(4) do

    print(val) \-- Outputs: 1, 2, 4, 8, 16

end

#### Explanation

The `for val in powers_of_two(4) do` syntax is a generic `for` loop. It relies on Lua's iterator protocol, which executes in three key steps: initialization, repeated calling, and termination.

**1\. Initialization** When Lua encounters `for val in powers_of_two(4) do`, it evaluates the expression after `in` **once** before entering the loop:

- It executes `powers_of_two(4)`.  
- This creates a closure containing its own local variable `exp = 0` and parameter `max_exponent = 4`.  
- The outer function returns the inner anonymous function, which Lua stores internally as the **iterator function**.

**2\. The Loop Step (Evaluation)** At the start of each iteration, Lua automatically calls the stored iterator function with no arguments:

- **1st call:** `exp` is `0` (≤4). Calculates 20=1, increments `exp` to `1`, returns `1`. `val` becomes `1`.  
- **2nd call:** `exp` is `1` (≤4). Calculates 21=2, increments `exp` to `2`, returns `2`. `val` becomes `2`.  
- **3rd call:** `exp` is `2` (≤4). Calculates 22=4, increments `exp` to `3`, returns `4`. `val` becomes `4`.  
- **4th call:** `exp` is `3` (≤4). Calculates 23=8, increments `exp` to `4`, returns `8`. `val` becomes `8`.  
- **5th call:** `exp` is `4` (≤4). Calculates 24=16, increments `exp` to `5`, returns `16`. `val` becomes `16`.

**3\. Termination**

- **6th call:** `exp` is now `5`. The condition `exp <= max_exponent` evaluates to `false`.  
- The inner function reaches its end without a `return` statement, implicitly returning `nil`.  
- When an iterator function returns `nil`, Lua interprets this as the signal that the loop has finished and terminates execution.

### Quick Lua Tip: Inspecting Upvalues

Lua's `debug` library allows you to inspect and modify upvalues captured by a closure:

local secret\_val \= 42

local function get\_secret()

    return secret\_val

end

\-- Get upvalue name and value (index 1\)

local name, value \= debug.getupvalue(get\_secret, 1\)

print(name, value) \-- Output: secret\_val    42