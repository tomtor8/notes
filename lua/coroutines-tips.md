# Coroutines tips and tricks

## Can you revive dead coroutines?

You can’t revive a dead coroutine. Once it finishes, its state is gone, and `resume` will always fail. The fix is to create a new coroutine from the same function:

```lua
local function letters()
  coroutine.yield("a")
  coroutine.yield("b")
end

local gen = coroutine.create(letters)
print(coroutine.resume(gen))  -- true   a
print(coroutine.resume(gen))  -- true   b
print(coroutine.resume(gen))  -- true   (finishes)
print(coroutine.resume(gen))  -- false  cannot resume dead coroutine

gen = coroutine.create(letters)   -- fresh coroutine, fresh state
print(coroutine.resume(gen))  -- true   a
```

The function is just code, so you can reuse it as often as you like. Each coroutine has its own state, though, so a restart always means creating a new one.

## Wrap coroutines

Wrapping doesn't return `true` or `false` as `coroutine.resume` does.

```lua
local co = coroutine.wrap(function()
  for i = 1, 3 do
    coroutine.yield(i * 10)
  end
end)

print(co(), co()) -- returns 10   20
```


