# Coroutines

**TL;DR:** A Lua coroutine is like a thread, but instead of running in parallel, it passes execution control manually. You can pause a function (`coroutine.yield`) and resume it later (`coroutine.resume`), preserving its internal state and variables.

## Definiton of coroutines

Unlike standard functions that run from start to finish, or OS threads that run in parallel using preemptive multitasking, Lua coroutines use **cooperative multitasking**. They run one at a time, yielding control explicitly when told to do so.

Think of it like a game turn: a function plays its turn, yields to the main program, and waits until you call it back to continue right where it left off.

## Core Coroutine Functions

1. `coroutine.create(fn)`: Creates a new coroutine from a function and returns a `thread` object.
2. `coroutine.resume(co, ...)`: Starts or continues the execution of coroutine `co`.
3. `coroutine.yield(...)`: Pauses the running coroutine, saving its state, and returns execution (plus optional values) back to `coroutine.resume`.
4. `coroutine.status(co)`: Returns `"suspended"`, `"running"`, or `"dead"`.

## Basic Code Example

```lua
-- Create a coroutine
local co = coroutine.create(function()
    print("Step 1: Started")
    coroutine.yield("Paused after step 1") -- Yields control and passes a string back
    print("Step 2: Resumed")
    return "Finished"
end)

print(coroutine.status(co)) -- "suspended"

-- Start the coroutine
local success, result = coroutine.resume(co)
print(result) -- Output: "Paused after step 1"
print(coroutine.status(co)) -- "suspended"

-- Resume execution
success, result = coroutine.resume(co)
print(result) -- Output: "Finished"
print(coroutine.status(co)) -- "dead"
```

### Step-by-step Output explanation

```plaintext
suspended
Step 1: Started
Paused after step 1
suspended
Step 2: Resumed
Finished
dead
```

1. **`print(coroutine.status(co))`**
   - Coroutine `co` has been created but not yet started.
   - **Prints:** `suspended`

2. **`local success, result = coroutine.resume(co)`**
   - Control enters the coroutine function.
   - **Prints:** `Step 1: Started`
   - `coroutine.yield("Paused after step 1")` pauses execution inside the coroutine and passes the message back to `resume`.
   - `success` becomes `true`, and `result` receives `"Paused after step 1"`.

3. **`print(result)`**
   - **Prints:** `Paused after step 1`

4. **`print(coroutine.status(co))`**
   - The coroutine is currently paused at `yield`.
   - **Prints:** `suspended`

5. **`local success, result = coroutine.resume(co)`**
   - Control resumes right after `coroutine.yield(...)`.
   - **Prints:** `Step 2: Resumed`
   - The function returns `"Finished"`, which terminates the coroutine.
   - `success` becomes `true`, and `result` receives `"Finished"`.

6. **`print(result)`**
   - **Prints:** `Finished`

7. **`print(coroutine.status(co))`**
   - The coroutine function has completed its body.
   - **Prints:** `dead`

## Key Use Cases in Scripting

- **State Machines / Game Loops:** Run non-blocking sequences like waiting N frames or handling step-by-step game AI logic without cluttering main loops with complex `if-else` states.
- **Generators / Iterators:** Yield values on the fly to conserve memory (like reading large files line-by-line).

## Passing parameters back and forth between coroutine.yield and coroutine.resume

**TL;DR:** Values passed to `coroutine.resume()` become either the arguments of the coroutine function (on first launch) or the return values of `coroutine.yield()` (on subsequent resumes). Conversely, values passed to `coroutine.yield()` are returned by `coroutine.resume()`.

Data flows in both directions when calling `resume` and `yield`:

- **`resume(...)` → Coroutine:** Sends data _into_ the coroutine.
- **`yield(...)` → Caller:** Sends data _out_ back to `coroutine.resume()`.

### Code Example

```lua
local co = coroutine.create(function(a, b)
    print(string.format("[Coroutine] Initial args received: a = %s, b = %s", tostring(a), tostring(b)))

    -- 1. Yield values back to the caller, and wait for new values from the next resume
    local c, d = coroutine.yield(a + b, "ready for next input")

    print(string.format("[Coroutine] Resumed with new args: c = %s, d = %s", tostring(c), tostring(d)))

    -- 2. Return final results when finishing execution
    return c * d, "done"
end)

print("--- 1st Resume ---")
-- First resume: Extra arguments go directly to function parameters 'a' and 'b'
local ok, sum, msg = coroutine.resume(co, 10, 20)
print(string.format("[Main] Received from yield: ok = %s, sum = %s, msg = '%s'", tostring(ok), tostring(sum), msg))

print("\n--- 2nd Resume ---")
-- Second resume: Extra arguments become the return values of 'coroutine.yield()'
local ok, product, status = coroutine.resume(co, 5, 4)
print(string.format("[Main] Received from return: ok = %s, product = %s, status = '%s'", tostring(ok), tostring(product), status))
```

**Output**

```plaintext
--- 1st Resume ---
[Coroutine] Initial args received: a = 10, b = 20
[Main] Received from yield: ok = true, sum = 30, msg = 'ready for next input'

--- 2nd Resume ---
[Coroutine] Resumed with new args: c = 5, d = 4
[Main] Received from return: ok = true, product = 20, status = 'done'
```

### Data Flow Breakdown

1. **First `coroutine.resume(co, 10, 20)`:**
   - Arguments `10, 20` populate function parameters `a, b`.
   - Inside, `coroutine.yield(30, "ready...")` pauses execution and passes `30, "ready..."` back to `resume`.
   - `resume` returns `true, 30, "ready..."` (`true` indicates success without errors).

2. **Second `coroutine.resume(co, 5, 4)`:**
   - Arguments `5, 4` become the return values assigned to `c, d` inside the coroutine where `coroutine.yield()` was waiting.
   - The function ends with `return c * d, "done"`.
   - Final `resume` returns `true, 20, "done"`.

## coroutine.wrap()

**TL;DR:** `coroutine.wrap` creates a coroutine and wraps it inside a plain Lua function. When you call this returned function, it resumes the coroutine directly without needing `coroutine.resume()`, and returns values directly (without the initial boolean status flag). However, if an error occurs, `coroutine.wrap` raises a regular Lua error instead of catching it safely like `coroutine.resume()`.

### Key Differences: `coroutine.create` vs. `coroutine.wrap`

| Feature                   | `coroutine.create`                           | `coroutine.wrap`                                  |
| ------------------------- | -------------------------------------------- | ------------------------------------------------- |
| **Return Value**          | Returns a `thread` object                    | Returns a callable **Lua function**               |
| **How to Resume**         | `coroutine.resume(co, ...)`                  | Call the function directly: `fn(...)`             |
| **Status / Yield Values** | Returns `boolean` (success) + yielded values | Returns yielded values directly (no boolean)      |
| **Error Handling**        | Safe: Returns `false` + error message        | Unsafe: Throws a standard Lua runtime error       |
| **Status Inspection**     | Works with `coroutine.status(co)`            | Cannot inspect status directly (no thread handle) |

### Code Comparison

#### Using `coroutine.create` (Manual Thread Handling)

```lua
local co = coroutine.create(function(name)
    print("Hello " .. name)
    local action = coroutine.yield("Ready")
    return "Done: " .. action
end)

-- Must pass thread object to resume; status boolean is prepended
local ok, msg = coroutine.resume(co, "Lua User")
print(ok, msg) -- true, "Ready"

local ok, result = coroutine.resume(co, "Scripting")
print(ok, result) -- true, "Done: Scripting"
```

#### Using `coroutine.wrap` (Functional Style)

```lua
local worker = coroutine.wrap(function(name)
    print("Hello " .. name)
    local action = coroutine.yield("Ready")
    return "Done: " .. action
end)

-- Call directly like a normal function; yields directly
local msg = worker("Lua User")
print(msg) -- "Ready"

local result = worker("Scripting")
print(result) -- "Done: Scripting"
```

**Output of both examples:**

```plaintext
Hello Lua User
true	Ready
true	Done: Scripting
```

### Error Handling Difference

When an error occurs inside a wrapped coroutine, it propagates up as a runtime error rather than returning `false`:

```lua
-- coroutine.create captures errors safely:
local co = coroutine.create(function() error("Something went wrong!") end)
local ok, err = coroutine.resume(co)
print(ok, err) -- false, "input:1: Something went wrong!"

-- coroutine.wrap raises an unhandled error:
local wrapped = coroutine.wrap(function() error("Something went wrong!") end)
-- wrapped() -- This line would crash the script unless wrapped in pcall()!
```

### Idiomatic Use Case: Custom Iterators

`coroutine.wrap` is frequently used to construct clean, custom iterators for `for ... in` loops because the returned function fits the iterator pattern natively:

```lua
local function range(start_val, end_val)
    return coroutine.wrap(function()
        for i = start_val, end_val do
            coroutine.yield(i)
        end
    end)
end

-- Uses coroutine.wrap directly inside a generic for loop
for num in range(1, 3) do
    print("Number:", num)
end
```

### Handling runtime errors using pcall

**TL;DR:** To safely handle runtime errors in a coroutine created with `coroutine.wrap`, wrap the invocation of the returned function inside `pcall()`. The first return value of `pcall` will be a boolean (`true` for success, `false` for error), followed by the yielded values or the error message.

#### Handling Errors with `pcall` and `coroutine.wrap`

Because calling a wrapped coroutine directly behaves like invoking a standard Lua function, any unhandled runtime error inside it will halt execution. Wrapping the call in `pcall` traps the error and prevents a script crash.

```lua
local wrapped_fn = coroutine.wrap(function(a, b)
    print("Step 1: Received", a, b)
    coroutine.yield("Step 1 complete")

    -- Intentionally cause a runtime error (attempting to perform arithmetic on a string)
    print("Step 2: Performing risky operation...")
    local result = a + "invalid_number"

    return result
end)

-- 1. First invocation (runs until first yield - no error)
local ok, result = pcall(wrapped_fn, 10, 20)
if ok then
    print("[Main] Success:", result) -- "Step 1 complete"
else
    print("[Main] Error captured:", result)
end

print("--- Resuming Coroutine ---")

-- 2. Second invocation (resumes and hits the runtime error)
local ok, result = pcall(wrapped_fn)
if ok then
    print("[Main] Success:", result)
else
    print("[Main] Error captured safely:")
    print("      ", result) -- Contains line number and error message
end

print("--- Script continues running smoothly ---")
```

**Output**

```plaintext
Step 1: Received	10	20
[Main] Success:	Step 1 complete
--- Resuming Coroutine ---
Step 2: Performing risky operation...
[Main] Error captured safely:
       input:7: attempt to perform arithmetic on a string value
--- Script continues running smoothly ---
```

#### Alternative: Handling Errors _Inside_ the Coroutine Body

Instead of wrapping the external call in `pcall`, you can also wrap specific risky code blocks _inside_ the coroutine function body itself. This keeps the coroutine alive and allows it to yield structured error details back to the caller:

```lua
local safe_worker = coroutine.wrap(function(data)
    -- Protect only the inner operation
    local ok, res = pcall(function()
        return data.value * 2
    end)

    if not ok then
        -- Yield a controlled error state back to caller
        coroutine.yield(false, "Internal processing failed: " .. tostring(res))
    else
        coroutine.yield(true, res)
    end
end)

local ok, status, payload = pcall(safe_worker, {}) -- passing empty table (data.value is nil)
print("Outer call succeeded?", ok)
print("Inner execution status:", status, "| Details:", payload)
```

## Example: task scheduler using coroutines

**TL;DR:** A task scheduler using Lua coroutines maintains a queue or list of active tasks (threads). On every loop iteration, it calls `coroutine.resume()` on each task. If a task needs to wait (e.g., for time to pass or an event to trigger), it yields its state; if it finishes, it gets removed from the queue.

### Basic Task Scheduler Implementation

This example builds a simple cooperative scheduler with a time-delay mechanism (`wait(seconds)`).


```lua
local Scheduler = {
    tasks = {}
}

-- Add a new function as a coroutine task
function Scheduler.spawn(fn, ...)
    local args = { ... }
    local co = coroutine.create(function()
        fn(table.unpack(args))
    end)
    table.insert(Scheduler.tasks, {
        co = co,
        wake_time = 0
    })
end

-- Yield execution until 'sec' seconds have passed
function Scheduler.wait(sec)
    coroutine.yield("wait", os.clock() + sec)
end

-- Main Event Loop
function Scheduler.run()
    while #Scheduler.tasks > 0 do
        local now = os.clock()

        -- Loop backwards so we can safely remove dead tasks with table.remove
        for i = #Scheduler.tasks, 1, -1 do
            local task = Scheduler.tasks[i]

            -- Check if task is ready to resume (wake time reached)
            if now >= task.wake_time then
                local ok, action, arg = coroutine.resume(task.co)

                if not ok then
                    -- Task crashed with a runtime error
                    print(string.format("[Scheduler Error] Task failed: %s", tostring(action)))
                    table.remove(Scheduler.tasks, i)

                elseif coroutine.status(task.co) == "dead" then
                    -- Task finished execution
                    table.remove(Scheduler.tasks, i)

                elseif action == "wait" then
                    -- Task yielded a delay requirement
                    task.wake_time = arg
                end
            end
        end

        -- Brief sleep simulation/pause to prevent 100% CPU usage
        -- (In real applications like UI/game engines, this aligns with frame ticks)
    end
    print("[Scheduler] All tasks completed.")
end
```

### Example Usage

Here, two separate tasks run concurrently without blocking each other:


```lua
-- Task 1: Runs every 0.5 seconds
Scheduler.spawn(function()
    for i = 1, 3 do
        print(string.format("[%s] Worker A - Step %d", os.date("%X"), i))
        Scheduler.wait(0.5)
    end
end)

-- Task 2: Runs every 0.2 seconds
Scheduler.spawn(function()
    for i = 1, 5 do
        print(string.format("  [%s] Worker B - Tick %d", os.date("%X"), i))
        Scheduler.wait(0.2)
    end
end)

-- Start the loop
Scheduler.run()
```

**Output**

Notice how Worker B ticks multiple times in between Worker A's steps, operating non-blockingly within the single-threaded process:


```plaintext
[05:38:45] Worker A - Step 1
  [05:38:45] Worker B - Tick 1
  [05:38:45] Worker B - Tick 2
  [05:38:46] Worker B - Tick 3
[05:38:46] Worker A - Step 2
  [05:38:46] Worker B - Tick 4
  [05:38:46] Worker B - Tick 5
[05:38:46] Worker A - Step 3
[Scheduler] All tasks completed.
```

### Key Components of the Event Loop

1. **Task Registry (`Scheduler.tasks`):** Stores active coroutines alongside state metadata (like `wake_time` or event tags).
2. **Cooperative Yield (`Scheduler.wait`):** Tasks voluntarily give up control to the scheduler and declare when or why they should wake up.
3. **Dispatch & Clean-up Loop (`Scheduler.run`):** Iterates over tasks, checks conditions, resumes eligible coroutines, and prunes finished ones (`status == "dead"`).
