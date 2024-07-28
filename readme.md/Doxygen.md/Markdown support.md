# Markdown Support

## Table of Contents
- [Standard Markdown](#standard-markdown)
  - [Paragraphs](#paragraphs)
  - [Headers](#headers)
  - [Block Quotes](#block-quotes)
  - [Lists](#lists)
  - [Code Blocks](#code-blocks)
  - [Horizontal Rulers](#horizontal-rulers)
  - [Emphasis](#emphasis)
  - [Strikethrough](#strikethrough)
  - [Code Spans](#code-spans)
  - [Links](#links)
    - [Inline Links](#inline-links)
    - [Reference Links](#reference-links)
  - [Images](#images)
  - [Automatic Linking](#automatic-linking)
- [Markdown Extensions](#markdown-extensions)
  - [Table of Contents](#table-of-contents-1)
  - [Tables](#tables)
  - [Fenced Code Blocks](#fenced-code-blocks)
  - [Header Id Attributes](#header-id-attributes)
  - [Image Attributes](#image-attributes)
- [Doxygen Specifics](#doxygen-specifics)
  - [Including Markdown Files as Pages](#including-markdown-files-as-pages)
  - [Treatment of HTML Blocks](#treatment-of-html-blocks)
  - [Code Block Indentation](#code-block-indentation)
  - [Emphasis and Strikethrough Limits](#emphasis-and-strikethrough-limits)
  - [Code Spans Limits](#code-spans-limits)
  - [Lists Extensions](#lists-extensions)
  - [Use of Asterisks](#use-of-asterisks)
  - [Limits on Markup Scope](#limits-on-markup-scope)
  - [Support for GitHub Alerts](#support-for-github-alerts)
- [Debugging Problems](#debugging-problems)

## Standard Markdown

### Paragraphs
Even before Doxygen had Markdown support it supported the same way of paragraph handling as Markdown: to make a paragraph you just separate consecutive lines of text by one or more blank lines.

An example:

Here is text for one paragraph.

We continue with more text in another paragraph.

### Headers
Just like Markdown, Doxygen supports two types of headers.

Level 1 or 2 headers can be made as the follows:

```
This is a level 1 header
========================

This is a level 2 header
------------------------
```

Alternatively, you can use `#`'s at the start of a line to make a header. The number of `#`'s at the start of the line determines the level (up to 6 levels are supported). You can end a header by any number of `#`'s.

Here is an example:

```
# This is a level 1 header

### This is level 3 header #######
```

### Block Quotes
Block quotes can be created by starting each line with one or more `>`'s, similar to what is used in text-only emails.

```
> This is a block quote
> spanning multiple lines
```

Lists and code blocks (see below) can appear inside a quote block. Quote blocks can also be nested.

Note that Doxygen requires that you put a space after the (last) `>` character to avoid false positives, i.e. when writing:

```
0  if OK\n
>1 if NOK
```

the second line will not be seen as a block quote.

### Lists
Simple bullet lists can be made by starting a line with `-`, `+`, or `*`.

```
- Item 1

  More text for this item.

- Item 2
  + nested list item.
  + another nested item.
- Item 3
```

List items can span multiple paragraphs (if each paragraph starts with the proper indentation) and lists can be nested. You can also make a numbered list like so:

```
1. First item.
2. Second item.
```

### Code Blocks
Preformatted verbatim blocks can be created by indenting each line in a block of text by at least 4 extra spaces.

```
This a normal paragraph

    This is a code block

We continue with a normal paragraph again.
```

Doxygen will remove the mandatory indentation from the code block. Note that you cannot start a code block in the middle of a paragraph (i.e. the line preceding the code block must be empty).

### Horizontal Rulers
A horizontal ruler will be produced for lines containing at least three or more hyphens, asterisks, or underscores. The line may also include any amount of whitespace.

Examples:

```
- - -
______
```

Note that using asterisks in comment blocks does not work. See [Use of Asterisks](#use-of-asterisks) for details.

Note that when using hyphens and the previous line is not empty you have to use at least one whitespace in the sequence of hyphens otherwise it might be seen as a level 2 header (see [Headers](#headers)).

### Emphasis
To emphasize a text fragment you start and end the fragment with an underscore or star. Using two stars or underscores will produce strong emphasis.

Examples:

```
 *single asterisks*

 _single underscores_

 **double asterisks**

 __double underscores__
```

See section [Emphasis and Strikethrough Limits](#emphasis-and-strikethrough-limits) for more info on how Doxygen handles emphasis/strikethrough spans slightly differently than standard/GitHub Flavored Markdown.

### Strikethrough
To strikethrough a text fragment you start and end the fragment with two tildes.

Examples:

```
 ~~double tilde~~
```

See section [Emphasis and Strikethrough Limits](#emphasis-and-strikethrough-limits) for more info on how Doxygen handles emphasis/strikethrough spans slightly differently than standard/GitHub Flavored Markdown.

### Code Spans
To indicate a span of code, you should wrap it in backticks (\`). Unlike code blocks, code spans appear inline in a paragraph. An example:

```
Use the `printf()` function.
```

To show a literal backtick or single quote inside a code span use double backticks, i.e.

```
To assign the output of command `ls` to `var` use ``var=`ls```.

To assign the text 'text' to `var` use ``var='text'``.
```

See section [Code Spans Limits](#code-spans-limits) for more info on how Doxygen handles code spans slightly differently than standard Markdown.

### Links
Doxygen supports both styles of make links defined by Markdown: inline and reference.

For both styles the link definition starts with the link text delimited by [square brackets].

#### Inline Links
For an inline link the link text is followed by a URL and an optional link title which together are enclosed in a set of regular parenthesis. The link title itself is surrounded by quotes.

Examples:

```
[The link text](http://example.net/)
[The link text](http://example.net/ "Link title")
[The link text](/relative/path/to/index.html "Link title")
[The link text](somefile.html)
```

In addition Doxygen provides a similar way to link a documented entity:

```
[The link text](@ref MyClass)
```

in case the first non-whitespace character of the reference is a `#` this is interpreted as a Doxygen link and replaced as a `@ref` command:

```
[The link text](#MyClass)
```

will be interpreted as:

```
@ref MyClass "The link text"
```

#### Reference Links
Instead of putting the URL inline, you can also define the link separately and then refer to it from within the text.

The link definition looks as follows:

```
[link name]: http://www.example.com "Optional title"
```

Instead of double quotes also single quotes or parenthesis can be used for the title part.

Once defined, the link looks as follows:

```
[link text][link name]
```

If the link text and name are the same, also:

```
[link name][]
```

or even:

```
[link name]
```

can be used to refer to the link. Note that the link name matching is not case-sensitive as is shown in the following example:

```
I get 10 times more traffic from [Google] than from
[Yahoo] or [MSN].

[google]: http://google.com/        "Google"
[yahoo]:  http://search.yahoo.com/  "Yahoo Search"
[msn]:    http://search.msn.com/    "MSN Search"
```

Link definitions will not be visible in the output.

Like for inline links, Doxygen also supports `@ref` inside a link definition:

```
[myclass]: @ref MyClass "My class"
```

### Images
Markdown syntax for images is similar to that for links. The only difference is an additional `!` before the link text.

Examples:

```
![Caption text](/path/to/img.jpg)
![Caption text](/path/to/img.jpg "Image title")
![Caption text][img def]
![img def]

[img def]: /path/to/img.jpg "Optional Title"
```

Also here you can use `@ref` to link to an image:

```
![Caption text](@ref image.png)
![img def]

[img def]: @ref image.png "Caption text"
```

The

 image size can be specified using the special attributes `width`, `height`, and/or `scale`.

Example:

```
![Caption text](/path/to/img.jpg width=20 height=30)
```

or:

```
![Caption text](/path/to/img.jpg scale=50)
```

### Automatic Linking
Unlike standard Markdown, Doxygen will automatically link http or ftp addresses.

Example:

```
This is a link to http://www.stack.nl/~dimitri/
```

This will become:

This is a link to http://www.stack.nl/~dimitri/

## Markdown Extensions

### Table of Contents
Doxygen provides support for tables of contents, even though these are not officially part of Markdown.

A table of contents is automatically generated when adding a line with:

```
\todoctableofcontents
```

A line with just `\sectiontitle` will act as a header and will be shown in the table of contents.

### Tables
To make tables in Markdown, Doxygen supports the same basic syntax as GitHub does.

The syntax is as follows:

```
First Header  | Second Header
------------- | -------------
First row     | Second row
```

Example:

```
| Heading 1 | Heading 2 | Heading 3 |
|-----------|------------|------------|
| Cell 1    | Cell 2     | Cell 3     |
```

Note that the headers must match the number of separators.

### Fenced Code Blocks
Doxygen supports the GFM (GitHub Flavored Markdown) fenced code blocks. Unlike the standard Markdown preformatted blocks which require indentation of 4 or more spaces, fenced code blocks are easier to write since they don't require indentation. The start of the block is marked by three backticks (\`) or three tildes (~) and the end of the block is marked by another set of three backticks or tildes.

Examples:

```
```
int x;
x = 10;
```
```

or

```
~~~
int x;
x = 10;
~~~
```

Fenced code blocks can also be preceded by a language name to enable syntax highlighting, for instance:

```
```cpp
int x;
x = 10;
```
```

### Header Id Attributes
Doxygen supports the use of custom header id attributes, which can be added to a header by appending `{#id}` to the header.

Example:

```
## Custom Header {#custom-header}
```

### Image Attributes
Doxygen supports the following image attributes in Markdown:
- `width`
- `height`
- `scale`
- `align`
- `alt`

Examples:

```
![Alt text](/path/to/img.jpg "Optional title" width=20 height=30 align=left alt="Alt text")
```

## Doxygen Specifics

### Including Markdown Files as Pages
Doxygen allows you to include Markdown files as pages in the documentation by using the `\page` or `\mainpage` command.

Examples:

```
\page markdown_example Example of Markdown

This is an example of including a Markdown file as a page.
```

### Treatment of HTML Blocks
Unlike Markdown, Doxygen does not treat HTML blocks differently, and you can include HTML directly in your Markdown files.

Example:

```
<div class="example">
  This is an example.
</div>
```

### Code Block Indentation
Doxygen requires at least 4 extra spaces of indentation for code blocks, similar to standard Markdown.

### Emphasis and Strikethrough Limits
Doxygen does not allow emphasis or strikethrough spans to be empty. For instance:

```
__ __  -> does not work
~~ ~~  -> does not work
```

### Code Spans Limits
Doxygen does not allow code spans to be empty. For instance:

```
`` ``  -> does not work
```

### Lists Extensions
Doxygen supports the same list syntax as standard Markdown, but it also allows lists to be nested.

Example:

```
- Item 1
  - Sub-item 1
  - Sub-item 2
- Item 2
```

### Use of Asterisks
Unlike standard Markdown, Doxygen does not support the use of `*` for emphasis or strong emphasis. Instead, you should use `_` and `__`.

### Limits on Markup Scope
Doxygen limits the scope of emphasis, strong emphasis, strikethrough, and code spans to single lines. For instance:

```
__text__ -> works
__text
text__   -> does not work
```

### Support for GitHub Alerts
Doxygen supports GitHub alert boxes using the same syntax as GitHub.

Example:

```
> **Note**
> This is a note.
```

## Debugging Problems
If you encounter issues with Markdown in Doxygen, you can use the `MARKDOWN_SUPPORT` setting in the Doxyfile to enable or disable Markdown support. Additionally, you can use the `EXTRACT_ALL` setting to extract all comments, which can help identify issues with your Markdown syntax.

```
