# **rawget()**

**TL;DR:** rawget(table, index) fetches the value stored directly in a Lua table key without invoking any \_\_index metamethod that might be attached to it.

### **How rawget Works**

When you access a table value using standard indexing like t\[key\], Lua checks if key exists in table t. If it doesn't, Lua looks for a metatable attached to t and triggers the \_\_index metamethod (if defined).  
rawget(table, index) skips the metatable lookup entirely and performs a direct, raw access.  
`-- Create a base fallback table`  
`local fallback = { default_key = "default_value" }`

`-- Create a main table with a metatable attached`  
`local t = { real_key = "real_value" }`  
`setmetatable(t, { __index = fallback })`

`-- Standard indexing triggers the __index fallback:`  
`print(t.default_key)        -- Output: "default_value"`

`-- rawget ignores __index and checks only the raw table 't':`  
`print(rawget(t, "default_key")) -- Output: nil`  
`print(rawget(t, "real_key"))    -- Output: "real_value"`

### **Key Use Cases**

> 1. **Preventing Infinite Loops in Metamethods:** Inside custom \_\_index function definitions, using standard indexing like t\[k\] triggers \_\_index again, causing infinite recursion. rawget(t, k) safely checks the underlying table.  
> 2. **Performance:** Bypassing metatable checks when you need to confirm if a table directly contains a key.  
> 3. **Internal Lib/Plugin Logic:** Useful when building Neovim/Lua modules or UI configurations where metatables wrap tables to handle defaults or dynamic fallbacks.

### **Grammar & Writing Feedback**

> * **Original:** *"Rawget (table, index) explain this lua func"*  
> * **Correction:** *"Could you explain the Lua function rawget(table, index)?"*  
> * **Note:** Lua function names are case-sensitive (rawget, not Rawget). Always use proper casing for standard functions to ensure valid code.

### **Spanish Language Tip (B1–B2)**

In Spanish technical contexts, the concept of "bypassing" or "skipping" a metamethod is often expressed using **omitir** or **saltarse**.

> * **Example:**  
  * *rawget se usa para **saltarse** el metamétodo \_\_index y obtener el valor real de la tabla.*  
  * (rawget is used to bypass the \_\_index metamethod and retrieve the raw value from the table.)  
> * **Vocab:** *Metamétodo* (Metamethod), *Tabla de origen* (Source table), *Recursión infinita* (Infinite recursion).

# **rawset()**

**TL;DR:** rawset(table, key, value) sets a key-value pair directly on a table while bypassing any \_\_newindex metamethod, pairing with rawget to read/write data safely without triggering custom hooks or infinite loops.

### **How rawset Works Alongside rawget**

When you assign a value to a non-existent table key using standard syntax (t\[key\] \= value), Lua triggers the \_\_newindex metamethod if one exists.  
rawset(table, key, value) assigns the value directly to the table, ignoring \_\_newindex.  
Here is how both functions work together to create a proxy or safe tracking wrapper:  
`local data = { count = 10 }`  
`local proxy = {}`

`-- Track writes and reads via metatable`  
`local mt = {`  
    `__index = function(t, key)`  
        `print("-> Reading key: " .. tostring(key))`  
        `-- Use rawget to read from 'data' without triggering loop/metamethods`  
        `return rawget(data, key)`  
    `end,`

    `__newindex = function(t, key, value)`  
        `print("-> Writing key: " .. tostring(key) .. " = " .. tostring(value))`  
        `-- Use rawset to write directly to 'data' bypassing __newindex`  
        `rawset(data, key, value)`  
    `end`  
`}`

`setmetatable(proxy, mt)`

`-- Writing through the proxy triggers __newindex and uses rawset underneath:`  
`proxy.count = 11      -- Output: -> Writing key: count = 11`  
`proxy.status = "ok"   -- Output: -> Writing key: status = ok`

`-- Reading through the proxy triggers __index and uses rawget underneath:`  
`print(proxy.count)    -- Output: -> Reading key: count`  
                      `--         11`

`-- Verify direct state in original data table`  
`print(data.status)    -- Output: ok`

### **Key Comparisons**

| Function | Standard Equivalent | Bypasses Metamethod | Primary Use Case |
| :---- | :---- | :---- | :---- |
| rawget(t, k) | t\[k\] (Read) | \_\_index | Safe reading / preventing recursion |
| rawset(t, k, v) | t\[k\] \= v (Write) | \_\_newindex | Safe writing / raw mutation |

### **Grammar & Writing Feedback**

> 4. **Original:** *"Show me how rawset works alongside rawget with a code example."*  
> 5. **Correction:** *"Show me how rawset works alongside rawget with a code example."*  
> 6. **Note:** The original sentence was grammatically sound and clear. Formatting code identifiers like rawset and rawget using backticks improves technical clarity.

### **Spanish Language Tip (B1–B2)**

To describe writing or updating values in a structure in Spanish, use **escribir** or **asignar**.

> * **Example:**  
  * *rawset nos permite **asignar** un valor directamente en la tabla sin activar el metamétodo \_\_newindex.*  
  * (rawset allows us to assign a value directly to the table without triggering the \_\_newindex metamethod.)  
> * **Vocab:** *Asignación de variables* (Variable assignment), *Control de acceso* (Access control), *Bucle infinito* (Infinite loop).

# **Implement an immutable table**

**TL;DR:** To build an immutable table in Lua, return a empty proxy table wrapped in a metatable where \_\_index uses rawget to fetch values from a hidden table, and \_\_newindex throws an error to block modifications.

### **Implementation**

The standard Lua pattern for a read-only table uses a hidden data table wrapped inside a blank proxy table. Any read operation falls back to \_\_index, while any write operation triggers \_\_newindex and raises an error.  
`local function make_readonly(target_table)`  
    `local proxy = {}`  
      
    `local mt = {`  
        `-- Pass-through read operations via rawget`  
        `__index = function(_, key)`  
            `return rawget(target_table, key)`  
        `end,`  
          
        `-- Intercept and block write operations`  
        `__newindex = function(_, key, value)`  
            `error("Attempt to modify read-only table at key: " .. tostring(key), 2)`  
        `end,`  
          
        `-- Optional: Protect the metatable from being changed or retrieved via getmetatable`  
        `__metatable = "This metatable is locked."`  
    `}`  
      
    `return setmetatable(proxy, mt)`  
`end`

`-- Example Usage`  
`local config = make_readonly({`  
    `host = "127.0.0.1",`  
    `port = 8080,`  
    `debug = true`  
`})`

`-- Reading values works as expected:`  
`print(config.host) -- Output: 127.0.0.1`  
`print(config.port) -- Output: 8080`

`-- Attempting to write throws an error:`  
`config.port = 9090`   
`-- Output: lua: example.lua:26: Attempt to modify read-only table at key: port`

### **Bypassing Read-Only Restrictions with rawset**

If you need an internal system function that is allowed to bypass the read-only constraint (e.g., during initialization or dynamic updates), use rawset directly on the underlying target\_table:  
`local function update_internal(readonly_proxy, hidden_table, key, value)`  
    `-- Bypasses the proxy's __newindex entirely`  
    `rawset(hidden_table, key, value)`  
`end`

### **Key Technical Details**

> 7. **The Empty Proxy:** The proxy table **must** remain empty. If keys exist directly inside proxy, Lua reads and writes them directly without triggering \_\_index or \_\_newindex.  
> 8. **The \_\_metatable Protection:** Setting \_\_metatable \= "Locked" prevents external code from running setmetatable(proxy, {}) to strip away the read-only protection.  
> 9. **Error Level 2:** Using error(msg, 2\) points the line number in the stack trace to the location where the caller attempted the illegal modification, rather than inside the \_\_newindex function itself.

### **Grammar & Writing Feedback**

> * **Original:** *"Show me how to implement an immutable / read-only table in Lua using rawget and rawset."*  
> * **Correction:** *"Show me how to implement an immutable/read-only table in Lua using rawget and rawset."*  
> * **Note:** Formatting function names as code blocks (rawget, rawset) maintains technical precision.

### **Spanish Language Tip (B1–B2)**

To describe immutability or write protection in Spanish software development, common terms are **inmutable**, **de solo lectura**, or **protegido contra escritura**.

> * **Example:**  
  * *Para crear una tabla **de solo lectura**, interceptamos la modificación mediante \_\_newindex e impedimos la **escritura**.*  
  * (To create a read-only table, we intercept modifications using \_\_newindex and prevent writing.)  
> * **Vocab:** *Tabla inmutable* (Immutable table), *Lanzar un error* (To throw an error), *Traza de la pila* (Stack trace).

