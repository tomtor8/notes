# Lua metatables examples

You can set a metatable to any value, not just a table.  
For tables use `setmetatable()`  
For any other value use `debug.setmetatable()`

## Use add operator for strings

Example code:

\`\`\`lua  
\#\!/usr/bin/env lua

local str1 \= "fir tado"  
local str2 \= 5

local add\_mt \= {  
    \_\_add \= function(x,y)  
        return string.format(\[\["%s" joined with "%s"\]\], x, y)  
    end,  
    \_\_le \= function(x,y) \-- less than or equal, not for 2 strings  
        return string.len(x) \<= y  
    end,  
}

debug.setmetatable(str1, add\_mt)

print(str1 \+ str2)

if str1 \<= str2 then  
    print("The first string is not longer than 5")  
else  
    print("The first string is longer than 5")  
end  
\`\`\`

Outputs: "first string" joined with "second string"  
It works also for integers and floats.

The \_\_le works with combinations of e.g. strings and numbers.  
Outputs: The first string is longer than 5\.

## Multiply strings

\`\`\`lua  
\#\!/usr/bin/env lua

local str1 \= "Test"

local add\_mt \= {  
    \_\_mul \= function(x,y)  
        local words \= {}  
        \-- repeat from 1 to 5, step 1  
        for \_ \= 1, y, 1 do  
            table.insert(words, x)  
        end  
        return table.concat(words, " ")  
    end  
}

debug.setmetatable(str1, add\_mt)

print(str1 \* 5\)

\`\`\`

Output: Test Test Test Test Test

## Multiply strings using CLI arguments

\`\`\`lua  
\#\!/usr/bin/env lua

\-- take 2 arguments, with defaults set below  
\-- then multiply the string with a number  
\-- arguments are strings by default

local str1 \= arg\[1\] or "Some String" \-- get the first command line argument  
\-- tonumber("somestring") returns nil and not an error  
local multiplicator \= tonumber(arg\[2\]) or 3 \-- get the second argument

local add\_mt \= {  
    \_\_mul \= function(x,y)  
        local words \= {}  
        \-- repeat from 1 to 5, step 1  
        for \_ \= 1, y, 1 do  
            table.insert(words, x)  
        end  
        \-- join the words  
        return table.concat(words, " \* ")  
    end  
}

debug.setmetatable(str1, add\_mt)

print(str1 \* multiplicator)

\`\`\`

./multiply-string.lua "Tom" 5  
Outputs: Tom \* Tom \* Tom \* Tom \* Tom