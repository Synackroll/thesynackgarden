---
{"dg-publish":true,"permalink":"/programming/beej-s-guides/beej-s-guide-to-c-programming/3-variables-and-statements/"}
---

# 3.1 Variables

Variable is a human-readable name that refers to some data in memory.

Memory is just a big array of bytes. Data is stored in that array. If a number is larger than a single byte, it is stored in multiple bytes. Each byte of memory is referred to by its index. The index into memory is called an *address*, *location*, or *pointer*.

In C, the value of a variable is in memory *somewhere* at some address. It's a pain to remember the value by its numeric address so we make up a name for it. That's all that a variable is.

## 3.1.1 Variable Names

Any characters 0-9, A-Z, a-z, and underscore for variable names.

- Can't start with digit
- Can't start with two underscores
- Can't start a variable name with an underscore followed by a captal A-Z.

## 3.1.2 Variable Types

<table>
<colgroup>
<col style="width: 23%">
<col style="width: 41%">
<col style="width: 35%">
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">Type</th>
<th style="text-align: right;">Example</th>
<th style="text-align: left;">C Type</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">Integer</td>
<td style="text-align: right;"><code>3490</code></td>
<td style="text-align: left;"><code>int</code></td>
</tr>
<tr class="even">
<td style="text-align: left;">Floating point</td>
<td style="text-align: right;"><code>3.14159</code></td>
<td style="text-align: left;"><code>float</code><a href="[footnotes.html#fn35](view-source:https://beej.us/guide/bgc/html/split/footnotes.html#fn35)" class="footnote-ref" id="fnref35" role="doc-noteref"></a></td>
</tr>
<tr class="odd">
<td style="text-align: left;">Character (single)</td>
<td style="text-align: right;"><code>'c'</code></td>
<td style="text-align: left;"><code>char</code></td>
</tr>
<tr class="even">
<td style="text-align: left;">String</td>
<td style="text-align: right;"><code>"Hello, world!"</code></td>
<td style="text-align: left;"><code>char *</code><a href="[footnotes.html#fn36](view-source:https://beej.us/guide/bgc/html/split/footnotes.html#fn36)" class="footnote-ref" id="fnref36" role="doc-noteref"></a></td>
</tr>
</tbody>
</table>

Before you can use a variable, you have to _declare_ that variable and tell C what type the variable holds. Once declared, the type of variable cannot be changed later at runtime.

Uninitialized variables have indeterminate value.

`printf()` hunts through the format string for a variety of special sequences which start with a percent sign (`%`) that tell it what to print. For example, if it finds a `%d`, it looks to the next parameter that was passed, and prints it out as an integer. If it finds a `%f`, it prints the value out as a float. If it finds a `%s`, it prints a string.

## 3.13 Boolean Types

In C, 0 means false and non-zero means true.

# 3.2 Operators and Expressions

