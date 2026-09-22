# file:read("a|l|L|n")

## `"l"` argument

Reads one line without the end / new line character. The `L` argument reads one line including the end / new line character.

local file \= assert(io.open("file.txt", "r"))

\-- You manually control reading and file closure

local line \= file:read("l") \-- Reads just ONE line

print(line)

local secondline \= file:read("l") \-- Reads the second line

print(secondline)

file:close()

## `"a"` argument

Reads the entire file from the file:seek() actual position. Default it the start of the file.

\-- Idiomatic way to read an entire file into memory:

local f \= assert(io.open("text.txt", "r"))

local content \= f:read("a")

f:close()

## Read file function

local function read\_file\_or\_default(path, default)

    \-- the \_ is an error message, we don't use it here

    local f, \_ \= io.open(path, "r")

    if not f then

        \-- returns default file content

        return default

    end

    local content \= f:read("a")

    f:close()

    return content

end

\-- Usage: Returns fallback string if file is missing

local config \= read\_file\_or\_default("missing.txt", "default\_setting \= true")

## `"n"` argument

Reads numbers one by one.

1 2

3

4

local file \= assert(io.open("file.txt", "r"))

\-- You manually control reading and file closure

local num1 \= file:read("n") \-- Reads number 1

print(num1)

local num2 \= file:read("n") \-- Reads number 2

print(num2)

file:close()  
