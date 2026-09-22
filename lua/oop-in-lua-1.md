# **Object oriented programming in Lua**

Lua doesn't have a built-in class keyword. Instead, OOP is implemented using tables as prototypes and setting \_\_index to point back to the class table.

### **1\. Basic Class & Methods**

A class is simply a table containing methods. When an instance is created, its metatable points \_\_index to the class table, allowing instances to inherit those methods.  
\-- Define the Class table  
local Vector \= {}  
Vector.\_\_index \= Vector \-- Look up missing keys in 'Vector'

\-- Constructor  
function Vector:new(x, y)  
    local instance \= { x \= x or 0, y \= y or 0 }  
    setmetatable(instance, Vector) – you can rather use setmetatable(instance, self)   
    return instance  
end

\-- Method (using ':' automatically passes 'self')  
function Vector:magnitude()  
    return math.sqrt(self.x^2 \+ self.y^2)  
end

\-- Usage  
local v \= Vector:new(3, 4\)  
print(v:magnitude()) \-- Output: 5

### **2\. Single Inheritance**

To create a subclass, set the subclass's metatable so its \_\_index points to the parent class.  
\-- Base Class: Animal  
local Animal \= {}  
Animal.\_\_index \= Animal

function Animal:new(name)  
    local instance \= { name \= name or "Unknown" }  
    setmetatable(instance, self) `– for inheritance to work you cannot use hardcoded (instance, Animal)`  
    self.\_\_index \= self  
    return instance  
end

function Animal:speak()  
    print(self.name .. " makes a sound.")  
end

\-- Subclass: Dog (inherits from Animal)  
local Dog \= Animal:new()

\-- Override/Add Subclass Constructor  
function Dog:new(name, breed)  
    local instance \= Animal:new(name) \-- Call parent constructor  
    instance.breed \= breed or "Unknown"  
    setmetatable(instance, self)  
    self.\_\_index \= self  
    return instance  
end

\-- Override Parent Method  
function Dog:speak()  
    print(self.name .. " barks\! (Breed: " .. self.breed .. ")")  
end

\-- Usage  
local generic \= Animal:new("Rex")  
generic:speak() \-- Output: Rex makes a sound.

local dog \= Dog:new("Buddy", "Golden Retriever")  
dog:speak()    \-- Output: Buddy barks\! (Breed: Golden Retriever)

### **How Lookup Works under the Hood**

When calling dog:speak():

> 1. Lua checks the dog instance table for "speak". Not found.  
> 2. Lua checks Dog via \_\_index. Found and executed.  
> 3. If Dog didn't define speak, Lua checks Animal via Dog's metatable \_\_index.

Here is a detailed, step-by-step breakdown of how class creation and prototype inheritance work under the hood in Lua based on the code provided.

**Step 1: Creating the Base Class Table (local Vector \= {})**

* **What happens:** Lua allocates a new empty table named Vector in memory.  
* **Under the hood:** In Lua, classes do not exist as distinct language constructs. The Vector table acts as a prototype object that holds shared methods and functions.

**Step 2: Setting up Prototype Lookup (Vector.\_\_index \= Vector)**

* **What happens:** The key \_\_index inside the Vector table is assigned to point back to the Vector table itself.  
* **Under the hood:** When a table uses Vector as its metatable, Lua checks the \_\_index key whenever a field or method isn't found in that table. Pointing Vector.\_\_index \= Vector ensures that missing keys on instances resolve directly to Vector's methods.

**Step 3: Instantiating Objects (Vector:new(x, y))**

* **Method syntax:** Using colon syntax function Vector:new(x, y) automatically passes Vector as an implicit first parameter named self.  
* **Instance table creation (local instance \= { x \= x or 0, y \= y or 0 }):** A fresh, isolated table is allocated in memory containing only instance-specific data (x and y fields).  
* **Connecting the Metatable (setmetatable(instance, Vector)):** Lua assigns the Vector table as the metatable for instance. This connects the instance's fallback mechanism to Vector.  
* **Return:** The newly constructed table is returned.

**Step 4: Method Invocation & Lookup (v:magnitude())**

* **Syntactic sugar:** Calling v:magnitude() is equivalent to writing v.magnitude(v).  
* **Under the hood search:**  
  1. Lua first searches v directly for the key "magnitude".  
  2. Because v only contains { x \= 3, y \= 4 }, the lookup fails.  
  3. Lua checks if v has a metatable. It finds Vector.  
  4. It inspects Vector's \_\_index field, which points to Vector itself.  
  5. Lua searches Vector\["magnitude"\], finds the function, and executes it with self bound to v.

**Step 5: Dynamic Subclassing (local Dog \= Animal:new())**

* **What happens:** Dog is instantiated by calling Animal:new().  
* **Under the hood:**  
  * Dog becomes a new instance table with a name field set to "Unknown".  
  * Because Animal:new() runs setmetatable(instance, self) and self.\_\_index \= self (where self is Animal), Dog inherits from Animal.  
  * Dog can now store its own separate constructor and methods (e.g., Dog:speak()), shadowing Animal's implementation if defined.

**Method Resolution Sequence in Inheritance**

When invoking dog:speak() on a subclass instance:

\[dog Instance Table\] \---\> Key "speak" missing?  
       |  
       v (via Metatable \-\> \_\_index)  
\[Dog Table\] \------------\> Found "speak"? YES \-\> Execute Dog:speak(dog)  
       |                                  \[cite: 1\]  
       v (if missing)  
\[Animal Table\] \---------\> Found "speak"? YES \-\> Execute Animal:speak(dog)  
\`\`\`\[cite: 1\]

By chaining metatables via \`\_\_index\`, Lua resolves methods upward through the prototype ch

Yes, it is completely valid to use `setmetatable(instance, self)` in `Vector:new(x, y)`.

oop-in-lua-1

In fact, using `self` is considered the standard and more flexible approach in Lua OOP idioms.

oop-in-lua-1

### **Why `self` works in `Vector:new(x, y)`**

When you call `Vector:new(3, 4)` using colon syntax, Lua automatically passes `Vector` as the implicit `self` argument to the function.

oop-in-lua-1

Inside the body of `Vector:new`:

* `setmetatable(instance, Vector)` explicitly references the global/outer `Vector` variable.  
   oop-in-lua-1  
* `setmetatable(instance, self)` dynamically references whatever table called the function (`Vector`).  
   oop-in-lua-1

Since `self` evaluates to `Vector` during that call, both lines produce the exact same outcome for basic instantiation.

oop-in-lua-1

### **Key Differences Under the Hood**

| Aspect | `setmetatable(instance, Vector)` | `setmetatable(instance, self)` |
| ----- | ----- | ----- |
| **Binding** | **Hardcoded** to `Vector`. oop-in-lua-1 | **Dynamic**, evaluates to whatever called `:new()`. oop-in-lua-1 |
| **Subclassing Support** | Breaks constructor inheritance. Subclasses cannot reuse `Vector:new()` directly to create subclass instances. | Enables constructor inheritance. Subclasses can call parent constructors without hardcoding parent types. |

### **Why `self` is Preferred for Extensibility**

If you ever decide to create a subclass of `Vector` (for example, a `Vector3D`), using `self` allows the subclass to inherit `Vector:new()` directly:

Lua  
\-- If Vector:new uses setmetatable(instance, self)  
local Vector3D \= Vector:new()

function Vector3D:new(x, y, z)  
    \-- 'self' inside Vector:new will be Vector3D when called via Vector3D:new  
    local instance \= Vector.new(self, x, y)  
    instance.z \= z or 0  
    return instance  
end

By using `self`, the newly instantiated object gets its metatable set to `Vector3D` rather than being forced back into a base `Vector`.