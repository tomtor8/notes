# Object oriented programming 2

## Private Fields (Data Hiding using Closures)

In Lua, true private fields are achieved using **closures and local variables** rather than storing keys directly on the instance table.

```lua
-- Factory function defining a BankAccount class with private state
local function BankAccount(initial_balance)
    -- Private variable (hidden inside the closure)
    local balance = initial_balance or 0

    local self = {}

    -- Public method to deposit
    function self:deposit(amount)
        if amount > 0 then
            balance = balance + amount
        end
    end

    -- Public method to check balance
    function self:get_balance()
        return balance
    end

    return self
end

-- Usage
local account = BankAccount(100)
account:deposit(50)

print(account:get_balance()) -- Output: 150
print(account.balance)     -- Output: nil (completely private!)
```

## Multiple Inheritance (Using Metatable `__index` Function)

Since Lua tables only have one metatable, multiple inheritance is achieved by setting `__index` to a **custom function** that searches through a list of parent classes sequentially.

```lua
-- Helper function to search parents for a key
local function search_parents(key, parents)
    for _, parent in ipairs(parents) do
        if parent[key] then
            return parent[key]
        end
    end
end

-- Define parent class 1
local Flyer = {}
function Flyer:fly()
    print(self.name .. " is flying!")
end

-- Define parent class 2
local Swimmer = {}
function Swimmer:swim()
    print(self.name .. " is swimming!")
end

-- Define child class with multiple parents
local Duck = {}

function Duck:new(name)
    local instance = { name = name or "Donald" }

    -- List of parent classes to inherit from
    local parents = { Duck, Flyer, Swimmer }

    setmetatable(instance, {
        __index = function(tbl, key)
            return search_parents(key, parents)
        end
    })

    return instance

end

-- Usage
local duck = Duck:new("Scrooge")
duck:fly() -- Output: Scrooge is flying! (Inherited from Flyer)
duck:swim() -- Output: Scrooge is swimming! (Inherited from Swimmer)
```

## Key Takeaways

- **Private Fields:** Store data as local variables inside factory functions. Do not attach them to self.
- **Multiple Inheritance:** Use a custom function for `__index` that iterates through an array/list of parent tables to locate methods dynamically.
