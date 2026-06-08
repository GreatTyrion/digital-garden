---
name: issue-writing
description: Creates and updates Digital Garden issues in this repository. Use when the user asks to write an issue, format article Markdown, generate mobile-friendly issue or article HTML, add Newfoundland map links, or update index.html and README.md for a new issue.
---

# Issue Writing

## When To Use

Use this skill for Digital Garden publishing work in this repository:

- Creating or updating `posts/issue-N/issue-N.md`.
- Creating `posts/issue-N/issue-N.html` as a mobile-friendly issue index page.
- Formatting source article Markdown in `posts/issue-N/`.
- Generating standalone mobile-friendly article HTML pages.
- Adding the Newfoundland map utility link.
- Updating `index.html` and `README.md` with the latest issue.

If the user asks for a highly designed standalone article page, also read the
`frontend-design` skill before designing the HTML.

## Core Workflow

1. Read the nearest prior issue as the format source.
   - Prefer `posts/issue-(N-1)/issue-(N-1).md`.
   - Prefer `posts/issue-(N-1)/issue-(N-1).html` for the issue index page style.
   - Read `index.html` and `README.md` before editing their issue lists.

2. Inspect all issue source files before writing.
   - Read article Markdown files named in the request.
   - Check whether corresponding `.html` pages already exist.
   - Check casing carefully, for example `Quantum-Mechanics.md` versus
     `quantum-mechanics.md`.

3. Normalize article Markdown before generating article HTML.
   - Use exactly one `#` title at the top.
   - Convert numbered section headings to `##`.
   - Use `---` between major sections.
   - Convert concept bullets to `- **Term**: explanation`.
   - Use Markdown blockquotes for quotations.
   - Preserve the article's argument and ordering unless the user asks to rewrite.

4. Generate mobile-first article HTML when requested.
   - Use a single static `.html` file with inline CSS and small vanilla JS.
   - Include `<meta name="viewport" content="width=device-width, initial-scale=1.0">`.
   - Add a reading progress bar and horizontal scroll navigation for long articles.
   - Use semantic sections with matching `href="#id"` links and `id` targets.
   - Include `@media (max-width: 940px)` and `@media (max-width: 640px)` or similar.
   - Include `prefers-reduced-motion` when animations are used.

5. Generate or update the issue index Markdown.
   - Mirror the previous issue's pattern: title, intro/summary, section bullets,
     detail links, `---` separators, and closing note.
   - Link each article to its generated `.html` file.
   - Include the Newfoundland map section when requested:
     `https://greattyrion.github.io/localRentalPlace/`.

6. Generate or update the issue index HTML.
   - Use the previous issue's visual language as the base.
   - Keep the page mobile-friendly: responsive hero, sticky horizontal section nav,
     card-like sections, large tap targets, and readable line length.
   - Link to article HTML pages and external map tools.

7. Update repository indexes.
   - Add the new issue as the first item in the `issues` array in `index.html`.
   - Add the new issue as the first row under "Latest Issues" in `README.md`.
   - Keep issue date, topics, preview, and file paths consistent across all places.

8. Verify.
   - Run `ReadLints` on edited Markdown and HTML files.
   - Check anchor pairs with search if HTML navigation was added.
   - Confirm `index.html` links to the issue HTML and `README.md` links to the
     issue Markdown.

## Style Rules

- Write user-facing content in Chinese unless the source article is English.
- Keep issue summaries concise and editorial, not mechanical.
- Preserve this repository's static publishing style: no build tooling unless the
  existing issue already requires it.
- Make HTML readable on phones first, then enhance for desktop.
- Do not edit plan files if the user says a plan is attached for reference.

## References

For templates, examples, and verification checks, see [REFERENCE.md](REFERENCE.md).
