---
layout: default
title: Style guide
date: 2021-03-04
---

# Style guide

The content in the website and blog posts uses a consistent style guideline. I also use the public [Microsoft Writing Style Guide](https://docs.microsoft.com/style-guide/welcome/).

## Text formatting

Text is formatted using these guidelines. Text formats aren't combined such as bold and code syntax
for a link.

| Format | Description | Markdown |
| ---- | ---- | ---- |
| **Bold** | User interface elements and input field names. <br> To improve readability and emphasize important information. | `**Bold**` |
| _Italics_ | User input, paths, folders, application, and filenames. <br> Used to introduce new terminology or keywords. | `_Italics_` |
| `Code` | Commands, code syntax, properties, language keywords, error codes. <br> Links that don't need to navigate to a site. | `` `Code` `` |

## Placeholder

Angle brackets `<>` are a placeholder for user input. On the first occurrence mention to replace the
placeholder value and brackets with the user's value.

For example, _C:\Users\\<user name\>_ is replaced with _C:\Users\david_.

## Heading syntax

Headings use three levels and are coded in Markdown with the hash (`#`) symbol. In general, if more
than three heading levels are needed, restructure the content.

```markdown
# H1 header

## H2 header

### H3 header
```

# H1 header

## H2 header

### H3 header

## Heading capitalization

Use sentence case capitalization for headings. Capitalize the first word in a heading and then only
use capitals for proper nouns or acronyms.

For example:

### This is a header

### Use Markdown and HTML to write articles

## Links

Locale such as `en-US` is excluded from links so that localized pages are displayed. For example,
`https://example.com/en-US` is replaced with `https://example.com`.

## Fictitious names

Names of people, company names, and domain names are fictitious. Any correlation with a real person,
company, or domain is a coincidence.

Examples use the reserved domain name: [example.com](https://en.wikipedia.org/wiki/Example.com).

## Contractions

Contractions are used to convey a less formal tone for the content.

| Formal | Contraction |
| ---- | ---- |
| are not | aren't |
| cannot | can't |
| do not | don't |
| does not | doesn't |
| is not | isn't |
| it is | it's |
| was not | wasn't |
| will not | won't |

## Alert boxes

This is the code needed to display alert boxes. The CSS that defines the box format is stored in
_assets/main.scss_. As needed, HTML is used in alert boxes for code syntax, links, line breaks,
bold, or italics.

### Tip

<div class="tip">
<b>Tip</b> <br>
This is a tip alert box.
</div>

### Note

<div class="note">
<b>Note</b> <br>
This is a note alert box.
</div>

### Warning

<div class="warning">
<b>Warning</b> <br>
This is a warning alert box.
</div>

### Danger

<div class="danger">
<b>Danger</b> <br>
This is a danger alert box.
</div>

## Lists

There are two types of lists:

- **Ordered**: Uses numbers and is used for steps in a procedure.
- **Unordered**: Uses bullet points to list related items.

In general, ordered and unordered list items end with a period, whether or not an item is a complete
sentence. Periods should be consistent within an entire list of items. An exception is when
punctuation affects readability such as when an item ends with a command.

### Ordered list

An ordered list uses `1.` for each item and the rendered site displays the correct numbers. Don't
use more than 10 items in an ordered list.

```markdown
1. Ordered list item one.
1. Ordered list item two.
1. Ordered list item three.
```

1. Ordered list item one.
1. Ordered list item two.
1. Ordered list item three.

### Unordered list

Unordered lists use a hyphen (`-`).

```markdown
- Unordered list item.
- Unordered list item.
- Unordered list item.
```

- Unordered list item.
- Unordered list item.
- Unordered list item.
