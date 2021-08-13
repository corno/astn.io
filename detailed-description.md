# Types
[railroad diagrams](./railroads.html)


# Multiline reformatting

Reformatting should be done according to the following rules:

* A line break is defined as follows:
  * a carriage return followed by a line feed (\r\n),
  * a line feed (\n),
  * or a carriage return not followed by a line feed (\r)
* A string line is defined as follows:
  * the array of code points starting after the start token ` up to the first line break in the string
  * the array of code points between 2 line breaks within the string
  * the array of code points after the last line break in the string up to the end token `
* A reference indentation is determined as follows: The block of tabs and spaces following the preceding linebreak in the ASTN text that is not within another multiline string. If there is no preceding linebreak, the reference indentation is an empty array of tabs and spaces
* For every string line in an multiline string do the following: If the string line starts with the reference indentation, strip this part of the line

A string line in a multiline string that does not start with the reference indentation is considered improperly formatted but nevertheless valid, and will not be reformatted by a processor, so all whitespace at the beginning of the line is considered part of that string.

# Canonical form
ASTN texts can conform to a cannonical form, but are not required to.
A cannonical form prescribes the part of the syntax of a text that is considered to have no semantic relevance, specifically:
* string wrapping
* code point representation within strings.
* whitespace

ASTN Generators should generate ASTN texts that conform to the canonical form.
## string wrapping
In ASTN, there are 3 flavors of string wrapping: `"`, `'` and `\``.
The following table shows what the canonical form is for each possible occurence of a string:

type | canonical form
--|--
reference to external schema | "
reference to external schema schema | "
keys in objects | "
keys in types (schema identifiers) | '
the option in a tagged union (schema identifiers') | '
strings values | ' or " or \` or unwrapped (`< 'valid' "also valid" also valid \`this too\` >`)

## code point representation within strings

1 if the code point is a ", a ' or a \` and not within a string wrapped in the same code point, it should not be escaped For example: this is not correct: `'a quote: \"'` and should be: `'a quote: "'`

2 `\n`, `\r`, `\t`, in quoted and apostrophed strings should be written in the shorthand escaped form, for example: `"\n"`

3 `\n`, `\r`, `\t`, in multiline strings should be the original characters, for example a tab: `\`  \``

4 All other code point that are not control characters should not be escaped. This is not correct: `"\u0041"`, it should be: `"A"`. This means that only the control characters may use the hexadecimal escape format

## whitespace
The canonical use of whitespace is described in the railroad diagrams. There are 3 options:

name | whitespace allowed | canonical form
--|--|--
ws:ss | yes | single space
ws:n | yes | no whitespace
ws:nl | yes | newline and proper indentation

The indentation level is incremented at the `ws:indentation+` locations and decremented at the  `ws:indentation-` locations

# Semantic/meaningful tokens
There are only 7 tokens in ASTN that have significance:
* 'Open Object' (either `verbose type` or `dictionary`)
* 'Open Array' (either `shorthand type` or `list`)
* 'Close Object'
* 'Close Array'
* 'Tagged Union Start'
* 'Multiline Sting' (string wrapped in backticks)
* 'Simple String (string with either no wrapping, quotes or apostrophes)