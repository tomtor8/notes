Lua's string.format() works like the C standard library function printf(), using a specifier sequence in the form '%\[flags\]\[width\]\[.precision\]specifier' to format data into text strings.

### **Specifier Reference**

**Strings & Characters**

> * **%s**: Formats a string (or converts numbers/booleans to their string representations).  
> * **%c**: Takes an integer ASCII/character code and outputs its single-character equivalent (e.g., 65 becomes 'A').  
> * **%q** *(Lua-specific)*: Formats a string safely enclosed in double quotes with escape sequences so it can be safely read back by the Lua interpreter (e.g., handles newlines, quotes, and control characters).

**Integers**

> * **%d** or **%i**: Signed decimal integer.  
> * **%u**: Unsigned decimal integer.  
> * **%o**: Unsigned octal (base 8\) integer.  
> * **%x** / **%X**: Unsigned hexadecimal (base 16\) integer using lowercase (abcdef) or uppercase (ABCDEF) characters.

**Floating-Point Numbers**

> * **%f**: Floating-point number in decimal notation (e.g., 3.141593).  
> * **%e** / **%E**: Floating-point number in exponential / scientific notation (e.g., 3.141593e+00 vs 3.141593E+00).  
> * **%g** / **%G**: Uses either %f or %e (%E), whichever is shorter, omitting trailing zeros automatically.  
> * **%a** / **%A**: Hexadecimal floating-point notation using lowercase (0x1.999999999999ap+1) or uppercase (0X1.999999999999AP+1).

**Pointers & Literal Percent**

> * **%p**: Formats a pointer or table/function address in memory (useful for debugging object references).  
> * **%%**: Escapes a literal percent sign (%).

### **Flags**

| Flag | Description | Example (string.format(...)) | Result |
| :---- | :---- | :---- | :---- |
| **\-** | Left-aligns the output within the given width (default is right-align). | string.format("%-5d", 42\) | "42 " |
| **\+** | Forces a leading plus sign \+ on positive numbers. | string.format("%+d", 42\) | "+42" |
| *(space)* | Inserts a single space before positive numbers if no sign is written. | string.format("% d", 42\) | " 42" |
| **0** | Pads the left side of the number with zeros instead of spaces. | string.format("%04d", 42\) | "0042" |
| **\#** | Alters output: adds 0x for %x/%X, 0 for %o, or forces a decimal point for floating points. | string.format("%\#x", 255\) | "0xff" |

### **Width and Precision**

Both width and precision accept up to **two digits** in Lua's internal parsing rules.

> * **Width** (e.g., %10s or %05d): Specifies the minimum total field width. If the output is shorter, it pads with spaces (or zeros if the 0 flag is set).  
> * **Precision** (e.g., %.2f or %.4s): Preceded by a dot ..  
  * For floating-point (%f, %e): Defines the exact number of decimal places.  
  * For strings (%s): Sets the maximum length to display (truncates longer strings).  
  * For integers (%d): Defines the minimum number of digits to display (adds leading zeros if needed).

\-- Format a float with fixed 2 decimal places  
local price \= string.format("$%.2f", 19.5) \-- "$19.50"

\-- Zero-padded 5-digit number  
local id \= string.format("%05d", 73\) \-- "00073"

\-- Safely format a multi-line string for code generation  
local code \= string.format("local path \= %q", "C:\\\\Lua\\\\script.lua\\n")  
\-- Output: local path \= "C:\\\\Lua\\\\script.lua\\n"  
