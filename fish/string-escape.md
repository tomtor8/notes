# `string escape` command

## Safely escape regex characters in search query

```fish
function safe_replace --description "Safely perform dynamic search and replace using regex escaping"
    # Parse arguments
    set -l search_term $argv[1]
    set -l replacement $argv[2]
    set -l input_text $argv[3..-1]

    # Check for required parameters
    if test (count $argv) -lt 2
        echo "Usage: safe_replace <search_term> <replacement> [input_text...]" >&2
        return 1
    end

    # Step 1: Safely escape regex metacharacters in the search query
    set -l safe_pattern (string escape --style=regex -- $search_term)

    # Step 2: Perform the replacement on input text or pipeline stdin
    if test -n "$input_text"
        string replace -ra -- $safe_pattern $replacement $input_text
    else
        string replace -ra -- $safe_pattern $replacement
    end
end
```

## Combine safe search patterns into a larger regex pattern

**TL;DR:** To combine `safe_pattern` into a larger regex like `^safe_pattern$`, use standard Fish variable expansion within quotes or string interpolation. Fish strings expand `$safe_pattern` into its literal escaped characters, making `string match -r "^$safe_pattern$"` work smoothly.

### **Building Complex Regexes with safe_pattern**

Since `string escape --style=regex` produces a standard string containing backslash-escaped characters (e.g., app.v1.0), you can concatenate it into any regex string in Fish using variable interpolation.

### Exact Full Match (`^` and `$`)

To ensure the input matches the string _exactly_ from start to finish:

```fish
set query "user.input[1]"
set safe_pattern (string escape --style=regex -- $query)

# Anchor to start (^) and end ($)
set full_regex "^$safe_pattern\$"

# Matching against target strings
string match -r -- $full_regex "user.input[1]"       # Match!
string match -r -- $full_regex "prefix_user.input[1]" # No match
```

**Note:** Escaping `$` as `$` inside double quotes in Fish is good practice to prevent Fish from treating `$` as a variable substitution operator if followed by valid variable characters.

### Word Boundaries and Prefix/Suffix Matching

You can combine safe_pattern with PCRE regex constructs like word boundaries (b), optional prefixes, or captured groups:

```fish
set term "C++"
set safe_pattern (string escape --style=regex -- $term)

# Match 'C++' at the beginning of a line or as a standalone word
set regex "(?i)^$safe_pattern\b"

string match -r -- $regex "C++ developer" # Match!
string match -r -- $regex "C++11"         # No match (b requires non-word boundary after '+')
```

### Combining Multiple Escaped Patterns with OR (`|`)

If you want to search for _any_ item from a list of arbitrary strings safely:

```fish
set terms "file.txt" "data[0]" "config.json"

# Escape each item and join with '|'
set escaped_terms
for t in $terms
    set -a escaped_terms (string escape --style=regex -- $t)
end

set group_pattern "("(string join "|" $escaped_terms)")"
# Produces: (file.txt|data[0]|config.json)

string match -r -- $group_pattern "Loading data[0]..." # Matches "data[0]"
```
