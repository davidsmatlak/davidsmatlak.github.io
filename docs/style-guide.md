---
layout: default
title: Style guide
date: 2021-02-28
---

# Style guide

The content in the website and blog posts uses a consistent style guideline. I also use the public [Microsoft Writing Style Guide](https://docs.microsoft.com/style-guide/welcome/).

## Text formatting

Text is formatted using these guidelines. Don't combine text formats such as bold and code syntax
for a link.

- **Bold**: User interface elements and input field names. As needed, to emphasize important
  information.
- _Italics_: User input, paths, folders, application, and filenames.
- `Code syntax`: Commands, code statements, properties, language keywords, error codes, and links
  that don't need to navigate to a site.

## Placeholder

Angle brackets `<>` are a placeholder for user input. On the first occurrence mention to replace the
placeholder value and brackets with the user's value.

For example, _C:\Users\\<user name\>_ is replaced with _C:\Users\david_.

## Heading capitalization

Use sentence case capitalization for headings. Capitalize the first word in a heading and then only
use capitals for proper nouns or acronyms.

For example:

- This is a header
- Use Markdown and HTML to write articles

## Links

Locale is excluded from links. For example, `en-US`. For example, `https://example.com` rather than
`https://example.com/en-US`.

## Fictitious names

Names of people, company names, and domain names are fictitious. Any correlation with a real person,
company, or domain are a coincidence.

Examples will use the reserved domain name: [example.com](https://en.wikipedia.org/wiki/Example.com).

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

There are two types of lists: ordered and unordered.

- Ordered lists use numbers and are used for steps in a procedure.
- Unordered lists use bullet points to list related items.

In general, ordered and unordered list items will end with a period, whether or not an item is a
complete sentence. An exception is if the punctuation affects readability, for example, when an item
ends with a command. Periods should be consistent within the entire list of items.
