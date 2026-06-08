# Issue Writing Reference

## Typical Issue Markdown Shape

```markdown
# Digital Garden 第N期

**本期导语**：第N期聚焦三件事：主题一、主题二，以及一个持续可用的纽芬兰地图入口。

## Article title

**简介**：One concise editorial paragraph explaining the article's purpose.

### 主要内容

- **Theme one**: concrete summary
- **Theme two**: concrete summary
- **Theme three**: concrete summary

**详细报告**：[阅读完整分析](./article-slug.html)

---

## 实用工具：纽芬兰地图入口

### 项目简介

在科技、新闻或科学话题之外，保留一个持续可用的本地工具入口。

### 功能特点

- **持续更新**：定期同步最新租房数据
- **地图交互**：直观查看区域位置与房源聚集度
- **本地聚焦**：覆盖纽芬兰圣约翰斯及周边城市

**地图网址**：<https://greattyrion.github.io/localRentalPlace/>

---

*本期 Digital Garden 就到这里，感谢你的阅读！*
```

## Article Markdown Normalization

Before:

```markdown
Article title

1. Section title

* Term: explanation
```

After:

```markdown
# Article title

## 1. Section title

- **Term**: explanation
```

Use these cleanup rules:

- Add spaces between numbers and Chinese units when readable: `1900 年`,
  `0.523 份`, `50%`.
- Standardize horizontal rules to `---`.
- Convert raw quote paragraphs to `> Quote`.
- Keep original claims intact unless the user asks for rewriting.

## Mobile HTML Checklist

Every generated article or issue HTML should include:

- `<!DOCTYPE html>` and `<html lang="zh-CN">`.
- UTF-8 charset and viewport meta.
- A concise `<title>` and `<meta name="description">`.
- Inline CSS using CSS variables for theme colors.
- Mobile-first layout with a narrow page width on small screens.
- Touch-friendly navigation links, usually horizontal scrolling.
- Readable body text: generous line height and no cramped paragraphs.
- Reading progress bar for long pages.
- Matching anchor pairs:
  - navigation link: `href="#section-id"`
  - section target: `id="section-id"`
- `@media (max-width: 940px)` and `@media (max-width: 640px)` or equivalent.
- `@media (prefers-reduced-motion: reduce)` when animation is present.

## Issue HTML Pattern

For `issue-N.html`, follow the previous issue index page:

- Masthead with back link and issue stamp.
- Hero title and one-paragraph summary.
- Tag row for major topics.
- Sticky section rail.
- One card-like article section per linked story.
- Bottom navigation back to homepage and previous issue.
- Footer noting that the page links to independent HTML articles.

## Repository Index Updates

In `index.html`, add the new issue at the start of `const issues = [...]`:

```javascript
{
    id: 'issue-N',
    title: 'Digital Garden 第N期',
    number: '#N',
    date: 'YYYY-MM-DD',
    topics: ['Topic A', 'Topic B', '纽芬兰地图'],
    preview: '第N期聚焦三件事：主题一、主题二，以及一个持续可用的纽芬兰地图入口。',
    file: 'posts/issue-N/issue-N.html'
},
```

In `README.md`, add the new issue as the first row:

```markdown
| [#N](posts/issue-N/issue-N.md) | YYYY-MM-DD | 聚焦主题一、主题二与纽芬兰地图入口 | 🤖, ⚛️, 🗺️ |
```

## Verification Searches

Use exact searches after editing:

- `issue-N` in `index.html`.
- `[#N]` in `README.md`.
- For HTML anchor checks:

```text
id="(intro|section-a|section-b)"|href="#(intro|section-a|section-b)"
```

Then run `ReadLints` on all edited files.
