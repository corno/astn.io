# Introduction

ASTN is a lightweight, text-based, language-independent generic syntax for editing data.
ASTN is short for 'Abstract Syntax Tree Notation' and is pronounced Aston(æstən).

It is heavily based on [JSON](https://www.ecma-international.org/publications-and-standards/standards/ecma-404/). In fact, it is a superset of JSON, meaning that every JSON text is a valid ASTN text.

If you find JSON a useful format for its intended use, namely data interchange, but not completely fitting for data editing, you might find ASTN useful.
ASTN is designed to stay as close as possible to JSON and only add the features that are considered indispensible for data editing.

One of the most directly visible benefits of ASTN is that syntax highlighting becomes much more useful.

# Example

![Example](./example.png)

# Changes to JSON
ASTN has the following changes to JSON:


## Schema support

ASTN is designed to maximally support editing of texts that must conform to a schema. The ASTN specification does not specify how schemas work, but it includes the fundamental concepts to make maximal use these schemas.
Let's give you some context: In the example above, all the blue words like 'projects' and 'tasks' are specified by the schema. The brown words like 'upgrade server' and 'kickoff' are not specified by a schema and free to choose by the person editing of the data. 
In other words, when editing data that conforms to a predefined schema, the person editing the data is bound by the schema rules in some places (metadata) and free in other places (instance data). This distinction is supported in ASTN by the follwing features:

* **support for alternative string wrapping** besides quoted strings, ASTN supports 3 additional types of strings

  * **aposthroped strings (')** - ASTN allows strings to be wrapped between `'`. Rationale: This makes it possible to differentiate between strings that represent fixed values (strings that cannot randomly be chosen by the editor of the text) vs variable strings (to be chosen by the editor). For example `'action holder': "Laura"` where `action holder` is fixed, and the `Laura` string is variable.
  * **non wrapped strings** - all sequences of non-whitespace-characters that are not within a string, a comment or contain a structural token are a token in itself, named `nonwrapped string`. (note: According to this definition the JSON literals `false`, `true`, `null` and JSON number are also `nonwrapped strings`).
  * **multiline strings (\`)** - see `Multiline string support`

* **`type` support**. There are 2 notations for `types`, called `verbose group` and `shorthand group`.

  * **`verbose groups`** are JSON `objects`, where the surrounding structural tokens  `{` and `}`, are replaced by `(` (U+0028) and `)` (U+0029). Rationale: a semantic distinction can be made between objects with keys determined by a schema (`types`) and objects with keys that can be chosen by the editor (`objects`). For example: `( 'name': "Alex", 'age': 10 )` vs `{ "Alex": 5, "Jack": 8 }`. In ASTN, the classic JSON `objects` (with `{` and `}`) are called `dictionaries`.
  * **`shorthand groups`** are JSON `arrays` in the same way that `verbose groups` are `objects`. In this case, instead of the tokens `[` and `]` as surrounding structural tokes, `<` (U+003C) and `>` (U+003E) are used. Rationale: a semantic distinction can be made between arrays whose elements are determined by a schema (`shorthand groups`) and arrays whose elements are determined by the person editing the data. For example: a group might require 3 values; name, age and vaccinated. This can be written in shorthand as follows:  `<"Alex", 10, true>`. Adding a 4th element would lead to a validation error. In ASTN, the classic JSON `arrays` (with `[` and `]`) are called `lists`.

* **a text can start with a header specifying the schema**. The header specifies the schema to which the text should conform. There are 2 options:

  * A reference to an extenal schema, indicated by an exclamation mark (U+0021), followed by a string. For example: `! "myorg/myschema1.0" ...body...`
  * An embedded schema (where the full schema is in the document). This is useful when you want to guarantee that a document will forever have its schema available. This is written with a double exclamation mark, followed by a string specifying a `schema schema` and then followed  a value. For example: `! ! "myorg/myschemaschema1.0" ...a value that represents the schema...  ...body...`. The `schema schema` string specifies to which schema schema the following (embedded) schema should conform. This embedded schema can then be used to validate the body that follows.

## Tagged Union support
Tagged unions allow to select a option out of an list of options defined in a schema and based on that option fill in the additional data that is required for that option.
Tagged unions are getting more and more common in programming languages. For data languages, they are very useful as well. For a thorough explanation, please read the [wikipedia page](https://en.wikipedia.org/wiki/Tagged_union).
In ASTN a tagged union is written by a a vertical bar (U+007C), followed by a string, followed by a value.

For example; if in a text a 'mode of transport' has to be selected, which can either be 'bicycle' or 'car', but in the latter case, a 'fuel type' is required, this can be represented as follows: `'mode of transport': | 'bicycle' ()` and `'mode of transport': | 'car' ( 'fuel type': "gas" )`


## Optional and Permissive Comma Use
* commas are optional. Rationale: When manually editing texts, keeping track of the commas is cumbersome.
* trailing commas are allowed. In JSON, commas are only allowed as separators **between** key-value pairs in objects and **between** values in arrays. Trailing commas are not permitted. In ASTN, wherever a comma can be used as a separator, it can also be present at the end. Rationale: making sure the last entry does not have a comma when the rest has is cumbersome.

## Multiline string support
In JSON, strings cannot span multiple lines. In some cases, this is limiting. You will have to write a string like `"first line\nsecond line"` or like this:
````
[
  "first",
  "second"
]
````
ASTN supports strings that span multiple lines. These strings are enclosed with a ` (backtick, U+0060).
These strings can contain horizontal tabs, carriage returns and line feeds.
These multiline strings are indentation aware. This means that an ASTN processors should reformat the string before further processing.

Example:
````
{
    'content': `this is a string that spans
    multiple lines.
    Indentation before each line should be ignored
    up to the point where the first line started.
    So, in the resulting string, the all lines except
    for the last one have no indentation and
     this line has an indentation of 1 space`
}
````

## Comment support
FIX

# Detailed specification description
[detailed description](./detailed-description.md)

# FAQ
## Why not use an existing syntax
Existing syntaxes are not a strict superset of JSON, do not provide all the features that are required for the schema integration that was the design goal for ASTN (Especially the tagged union is not widely available) and/or provide way more features than that are required for the design goals of ASTN.
We looked at the following syntaxes:
* CSV
* TOML
* XML
* YAML
* JSON5
* HJSON
