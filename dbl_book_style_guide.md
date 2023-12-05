#DBL Book Style Guide

## Contents
- [Headings](#headings)
- [Code](#code)
- [Keywords](#keywords)
- [Product names](#product-names)

## Headings

Headline style for the heading at the top of files (i.e., a single #), and then, for subheadings in files ("## Second-level", "### Third-level", etc.) capitalize the first letter of the first word only, as they are in this style guide. 

## Code 

**Code in text.** (frequently referred to as "inline code") should be enclosed in backticks--e.g., This is an assignment in Synergy: `name="Zeno"`. 

**Code blocks.** Enclosed in triple-backtick lines, and the first of these should end in dbl if we'll want it to compile in the future. For example, the following starts with ```dbl:

```dbl
public class MyClass, void
public method MyMethod
    name, string
proc
    name = "Zeno"
endclass
```
If a code example isn't intended to be compiled as shown, it should be marked with `dbl,ignore,does_not_compile`. For example, the following starts with ```dbl,ignore,does_not_compile:

```dbl,ignore,does_not_compile
for variable = value, value2, ... do
begin
    ; statement
end
```

**Tokens in code.** If something in a code sample is to be replaced by a value, enclose it in curly quotes—e.g., `a{size}`. When possible, there should be a snippet example that shows the replacement. For example: 

"...`a{size}` (e.g., `a10` for an alpha with a length of 10)."

## Keywords
Keywords should be in all caps (e.g., USING-RANGE). Don’t use backticks unless the keyword is in code.
## Product names

**Traditional DBL.** "Traditional DBL" is considered a product name and should have initial caps.
