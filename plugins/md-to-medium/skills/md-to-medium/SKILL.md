# md-to-medium: Convert Markdown to Medium-publishable HTML

Convert a `.md` file into an `.html` file using Medium's internal `graf` CSS classes, so the content can be dragged and dropped directly into Medium's editor with formatting intact.

## How to invoke

```
/md-to-medium <path-to-file.md>
```

Example:
```
/md-to-medium BLOG.md
/md-to-medium /Users/manan/Vibes/website-revamp/BLOG.md
```

## What you do

1. Read the input `.md` file
2. Convert it to Medium-compatible HTML (rules below)
3. Wrap in a minimal HTML shell with the required Medium styles
4. Write the output as `<original-name>.html` in the same directory
5. Tell the user how to use it

---

## Conversion rules

### Element ordering (critical)

**The `<h1>` MUST be the first element in the body.** Medium treats the first element as the story title. Any content placed before the H1 (series callouts, blockquotes, subtitles) will be grabbed as the title instead.

Rule: if the markdown has a callout/series line before the `# Title`, move it to immediately *after* the H1 in the HTML output.

### Headings

```
# Title       →  <h1 class="graf graf--h1">Title</h1>
## Section    →  <h2 class="graf graf--h2">Section</h2>
### Sub        →  <h3 class="graf graf--h3">Sub</h3>
```

Strip the leading `>` blockquote series header line from its original position and place it as an italicized `<p>` immediately after the `<h1>`.

### Paragraphs

Each non-empty, non-code, non-heading line block becomes:
```html
<p class="graf graf--p">text</p>
```

### Bold and italic (inline)

```
**text**   →  <strong class="markup--strong markup--p-strong">text</strong>
*text*     →  <em class="markup--em markup--p-em">text</em>
```

### Inline code

```
`code`  →  <code class="markup--code markup--p-code">code</code>
```

### Links

```
[text](url)  →  <a href="url" class="markup--anchor markup--p-anchor" rel="noopener">text</a>
```

### Code blocks

Medium has a pre/code block. Wrap fenced code blocks (``` ... ```) as:
```html
<pre class="graf graf--pre">
<code>...code content here...</code>
</pre>
```

Strip the language hint (```js, ```jsx, etc.) from the opening fence line.

### Blockquotes

```
> text  →  <blockquote class="graf graf--blockquote"><p>text</p></blockquote>
```

### Horizontal rules

```
---  →  (omit entirely — Medium has no hr equivalent; just ensure a blank line between sections)
```

### Unordered lists

```
- item  →  <ul class="postList"><li class="graf graf--li">item</li></ul>
```

Group consecutive `- ` lines into one `<ul>`.

### Series/callout blockquote (the `> **Label** · ...` pattern)

Move after the H1. Preserve bold on the label, rest as normal paragraph text. No `<em>` wrapper:
```html
<p class="graf graf--p"><strong class="markup--strong markup--p-strong">Building with Claude</strong> · Part 1 of an ongoing series ...</p>
```

Do NOT wrap in `<em>` — Medium renders it as italic which doesn't match the intended style.

### Do not include HTML comments

Comments with `--` in their syntax (`<!-- -->`) can leak into clipboard paste in some browsers. Omit all comments from the output HTML.

---

## Output HTML shell

Wrap everything in this minimal shell so the browser renders it and the user can select-all and paste:

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<style>
  body { font-family: Georgia, serif; max-width: 680px; margin: 40px auto; line-height: 1.8; }
  pre { background: #f4f4f4; padding: 16px; overflow-x: auto; }
  code { font-family: monospace; font-size: 0.9em; }
  h1 { font-size: 2em; }
  h2 { font-size: 1.4em; margin-top: 2em; }
  blockquote { border-left: 3px solid #ccc; margin-left: 0; padding-left: 20px; color: #555; }
</style>
</head>
<body>
<!-- PASTE INTO MEDIUM: Open this file in a browser, Cmd+A, Cmd+C, then paste into Medium's editor -->
[converted content here]
</body>
</html>
```

---

## Instructions to give the user after writing the file

```
Done. Output written to: <filename>.html

To publish on Medium:
1. Open <filename>.html in your browser (double-click or `open <filename>.html`)
2. Cmd+A to select all content
3. Cmd+C to copy
4. Go to medium.com/new and paste (Cmd+V) into the editor

Code blocks will need one manual step: Medium doesn't preserve <pre> on paste.
For each code block, delete the pasted version, click the + button in Medium's
editor, choose "Code", and paste the raw code in.

Everything else (headings, bold, italic, links, blockquotes) should carry over automatically.
```
