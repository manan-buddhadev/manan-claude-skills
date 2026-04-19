# md-to-substack: Convert Markdown to Substack-publishable HTML

Convert a `.md` file into an `.html` file optimized for Substack's rich text editor, based on the conversion rules used by https://md-to-substack.netlify.app (github.com/simpolism/md-to-substack).

## How to invoke

```
/md-to-substack <path-to-file.md>
```

Example:
```
/md-to-substack BLOG.md
/md-to-substack /Users/manan/Vibes/website-revamp/BLOG.md
```

## What you do

1. Read the input `.md` file
2. Convert it to Substack-compatible HTML (rules below)
3. Wrap in a minimal HTML shell for browser rendering
4. Write the output as `<original-name>-substack.html` in the same directory
5. Tell the user how to use it

---

## Conversion rules

### Element ordering (critical)

**The `<h1>` MUST be the first element.** Substack uses the first heading as the post title. Any content before it (series callouts, blockquotes) gets treated as the title instead.

If the markdown has a callout or series line before the `# Title`, move it to immediately after the H1 in the HTML output.

### Headings

```
# Title    →  <h1>Title</h1>
## Section →  <h2>Section</h2>
### Sub     →  <h3>Sub</h3>
```

No custom classes needed — Substack's editor reads standard HTML heading tags.

### Series/callout blockquote (the `> **Label** · ...` pattern)

Move after H1. Use `<strong>` for the label, normal text for the rest. No `<em>` wrapper:

```html
<p><strong>Building with Claude</strong> · Part 1 of an ongoing series ...</p>
```

### Paragraphs

Each block of text becomes a standard `<p>`:

```html
<p>text here</p>
```

Substack's editor respects paragraph breaks on paste.

### Line breaks within paragraphs

Single line breaks in markdown → `<br>` inside the `<p>`.

For better Substack compatibility, convert `<br>` to separate `<p>` tags (the "extra spacing" behavior from the tool):

```
Line one\nLine two  →  <p>Line one</p><p>Line two</p>
```

### Bold and italic

```
**text**  →  <strong>text</strong>
*text*    →  <em>text</em>
```

### Inline code

```
`code`  →  <code>code</code>
```

### Links

**Always add `target="_blank"`** — Substack requires links to open in new tabs:

```
[text](url)  →  <a href="url" target="_blank" rel="noopener">text</a>
```

### Code blocks

```html
<pre><code>...code content here...</code></pre>
```

Strip the language hint (` ```js `, ` ```jsx `, etc.) from the opening fence line.

Escape HTML entities inside code blocks:
- `<` → `&lt;`
- `>` → `&gt;`
- `&` → `&amp;`

### Blockquotes

```
> text  →  <blockquote><p>text</p></blockquote>
```

### Horizontal rules

```
---  →  <hr>
```

Substack renders `<hr>` as a section divider.

### Unordered lists

```html
<ul>
  <li>item one</li>
  <li>item two</li>
</ul>
```

### Ordered lists

```html
<ol>
  <li>item one</li>
  <li>item two</li>
</ol>
```

### Smart quotes

Convert straight quotes to curly quotes (the tool enables this by default via marked-smartypants):

```
"text"  →  "text"
'text'  →  'text'
```

Do NOT apply smart quotes inside `<code>` or `<pre>` blocks.

---

## Output HTML shell

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<style>
  body { font-family: 'Times New Roman', serif; max-width: 680px; margin: 40px auto; line-height: 1.8; color: #111; }
  h1 { font-family: 'Helvetica Neue', sans-serif; font-size: 2em; }
  h2 { font-family: 'Helvetica Neue', sans-serif; font-size: 1.4em; margin-top: 2em; }
  h3 { font-family: 'Helvetica Neue', sans-serif; font-size: 1.1em; margin-top: 1.5em; }
  pre { background: #f4f4f4; padding: 16px; overflow-x: auto; border-radius: 4px; }
  code { font-family: monospace; font-size: 0.9em; }
  blockquote { border-left: 3px solid #ccc; margin-left: 0; padding-left: 20px; color: #555; }
  hr { border: none; border-top: 1px solid #ddd; margin: 2em 0; }
  a { color: #1a1a1a; }
</style>
</head>
<body>
[converted content here]
</body>
</html>
```

The design mirrors the tool's NYT-inspired editorial style (Helvetica Neue for UI/headers, Times New Roman for body).

---

## Do not include HTML comments

Comments with `--` in their syntax (`<!-- -->`) can leak into clipboard paste in some browsers. Omit all comments from the output HTML.

---

## Instructions to give the user after writing the file

```
Done. Output written to: <filename>-substack.html

To publish on Substack:
1. Open <filename>-substack.html in your browser  →  open <filename>-substack.html
2. Cmd+A to select all, Cmd+C to copy
3. Go to your Substack draft and paste (Cmd+V)

Notes:
- Code blocks need a manual step: Substack doesn't preserve <pre> on paste.
  For each code block, delete what pasted and use Substack's code block button instead.
- Links will open in new tabs (target="_blank" is set).
- Smart quotes are applied automatically.
```
