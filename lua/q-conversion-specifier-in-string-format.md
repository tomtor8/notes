The **%q** conversion specifier in Lua’s string.format() safely formats a string value into a **valid Lua literal string constant**, complete with surrounding double quotes and proper escape sequences.  
It is uniquely suited for writing dynamic Lua scripts, saving state to disk, or building commands to pass safely through shells.

### **Key Mechanics**

> * **Encloses in Quotes:** Automatically wraps string outputs in "".  
> * **Escapes Control Characters:** Converts non-printable or structural characters—such as newlines (\\n), tabs (\\t), quotes ("), backslashes (\\), and binary data—into explicit escape sequences (\\", \\\\, \\127).  
> * **Handles nil, Booleans, and Numbers (Lua 5.3+):** Returns nil, true, false, or the formatted number without quotes, maintaining valid code syntax.

### **Practical Examples**

**1\. Escaping Special Characters & Quotes**  
`local user_input = 'Hello "world"!\nNext line.'`

`-- Standard string format (%s) vs Lua code literal (%q)`  
`print(string.format("%s", user_input))`  
`-- Hello "world"!`  
`-- Next line.`

`print(string.format("%q", user_input))`  
`-- "Hello \"world\"!\nNext line."`

**2\. Dynamically Generating Lua Configuration Files** When writing settings or script state out to a file, using %s breaks if the value contains quotes or newlines. %q ensures the output stays syntactically sound:  
`local file_path = "C:\\Program Files\\My App\\config.txt"`

`-- Generates valid code line: local path = "C:\\Program Files\\My App\\config.txt"`  
`local code_line = string.format("local path = %q", file_path)`

### **%s vs %q Comparison**

| Feature | %s | %q |
| :---- | :---- | :---- |
| **Output Type** | Raw string value | Valid Lua string code constant |
| **Quotes Added?** | No | Yes (enclosed in double quotes) |
| **Newlines / Escapes** | Evaluates visually (\\n breaks line) | Escaped as raw symbols (\\n, \\", \\\\) |
| **Primary Use Case** | Human-readable text display | Code generation, serialization, shell args |

