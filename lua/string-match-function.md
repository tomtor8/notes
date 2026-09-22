### **The Syntax**

The basic structure of the function is:

Lua

string.match(s, pattern, \[init\])

> * **s**: The target string you want to search.  
> * **pattern**: The string pattern you are looking for.  
> * **init** (optional): The numerical index where the search should start (default is 1).

### ---

**2\. How it Returns Values**

One of the most important things to understand is what string.match actually gives you back:

> 1. **If the pattern has no captures (parentheses):** It returns the entire matched part of the string.  
> 2. **If the pattern has captures ():** It returns only the values inside the parentheses.  
> 3. **If no match is found:** It returns nil.

#### **Example: Basic Match**

Lua

local filename \= "01\_Taurus.mp3"  
local result \= string.match(filename, "Taurus")

print(result) \-- Output: Taurus

#### **Example: Using Captures**

As a Neovim user, you might want to extract just the extension from a file:

Lua

local filename \= "init.lua"  
local extension \= string.match(filename, "%.(%a+)$")

print(extension) \-- Output: lua

**Note:** In Lua patterns, % is the escape character (like \\ in standard Regex). %. matches a literal dot.

### ---

**3\. Lua Patterns vs. Standard Regex**

It is vital to remember that Lua does **not** use standard Regex (PCRE). It uses its own simplified pattern-matching system. This makes it very fast and lightweight for embedded systems (and editors like Neovim).

| Pattern | Matches |
| :---- | :---- |
| %d | Any digit (0-9) |
| %a | Any letter (a-z, A-Z) |
| %w | Any alphanumeric character |
| %s | Any whitespace character |
| %p | Any punctuation character |
| . | Any character |
| \+ | 1 or more repetitions (greedy) |
| \- | 0 or more repetitions (non-greedy) |

### ---

**4\. Practical Arch Linux Example**

Imagine you are writing a script to parse your pacman log to find when a specific package was upgraded:

Lua

local log\_line \= "\[2025-06-08T12:00\] \[ALPM\] upgraded neovim (0.10.0 \-\> 0.10.1)"

\-- Capture the package name and the new version  
local pkg, version \= string.match(log\_line, "upgraded (%w+) %((%d+%.%d+%.%d+) %-\>")

if pkg then  
    print("Package: " .. pkg)    \-- Output: neovim  
    print("New Ver: " .. version) \-- Output: 0.10.1  
end

### ---

**Quick Comparison**

| Function | Result |
| :---- | :---- |
| string.find | Returns the **indices** (start and end position). |
| string.match | Returns the **actual text** found. |
| string.gsub | **Replaces** the matched text with something else. |

