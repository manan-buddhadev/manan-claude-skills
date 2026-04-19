# manan-claude-skills

A collection of Claude Code skills and plugins for writing, publishing, and agentic coding workflows. Built and maintained by [Manan Buddhadev](https://manan-buddhadev.github.io/).

Part of the [Building with Claude](https://github.com/manan-buddhadev/manan-claude-skills) series.

---

## Skills

### `md-to-medium`
Converts a Markdown file to Medium-publishable HTML using Medium's internal `graf` CSS classes. Open the output in a browser, Cmd+A, paste into `medium.com/new`.

### `md-to-substack`
Converts a Markdown file to Substack-compatible HTML. Applies smart quotes, sets links to open in new tabs, and uses Substack-optimized paragraph formatting.

---

## Install as plugins

```
/plugin marketplace add manan-buddhadev/manan-claude-skills
```

Then install individual skills:

```
/plugin install md-to-medium@manan-claude-skills
/plugin install md-to-substack@manan-claude-skills
```

---

## Install as standalone skills

Clone directly into your Claude skills directory:

```bash
git clone https://github.com/manan-buddhadev/manan-claude-skills.git /tmp/manan-claude-skills
cp -r /tmp/manan-claude-skills/plugins/md-to-medium/skills/md-to-medium ~/.claude/skills/
cp -r /tmp/manan-claude-skills/plugins/md-to-substack/skills/md-to-substack ~/.claude/skills/
```

Then invoke with:

```
/md-to-medium BLOG.md
/md-to-substack BLOG.md
```

---

## Usage

```
/md-to-medium <path-to-file.md>
/md-to-substack <path-to-file.md>
```

Each skill reads the input file, converts it to platform-optimized HTML, and writes the output alongside the source file. Instructions for pasting into the editor are printed after the file is written.

**Note on code blocks:** Neither Medium nor Substack preserves `<pre>` on paste. For each code block, delete what pasted and use the platform's native code block button instead.

---

## License

MIT
