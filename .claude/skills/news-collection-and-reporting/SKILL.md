---
name: news-collection-and-reporting
description: Collect and compile regional news into structured Markdown reports with optional mobile-friendly HTML generation. Use this skill when users ask to gather news, summarize current events, create news digests, or write news reports for a specific region (such as Newfoundland, Canada or other areas). Triggers on requests like "collect news from [region]", "summarize recent events in [area]", "create a news report", or "what happened in [location] recently".
---

# News Collection and Reporting

Collect, curate, and compile regional news into professional Markdown reports with consistent formatting and journalistic quality. Optionally generate mobile-friendly HTML pages.

## Workflow

1. **Identify scope**: Confirm target region, time range (default: past 3 weeks), and topic areas (education, economy, energy, society, etc.)

2. **Collect news**: Use web search tools (Exa, web_search) to gather news from:
   - Major news outlets (CBC, SaltWire, NTV, local sources)
   - Government press releases
   - Industry publications
   - Reputable wire services

3. **Curate content**: Select 15 significant events covering diverse topics. Prioritize:
   - Impact on local community
   - Policy implications
   - Economic significance
   - Human interest value

4. **Write Markdown report**: Format each event using the standard structure below.

5. **Offer HTML generation**: After completing the Markdown report, ask:
   > "是否需要生成移动端友好的 HTML 网页版本？(Y/N)"
   
   If yes, generate HTML using the template in `assets/news-report-template.html`.

## Event Structure

For each news event, include:

| Field | Content |
|:------|:--------|
| **📰 事件标题** | Clear, informative headline |
| **📅 发生时间** | Specific date (YYYY年MM月DD日) |
| **🧩 事件概要** | Background context and key details (2-3 sentences) |
| **🌍 涉及机构** | Organizations, departments, or entities involved |
| **🔍 影响或意义** | Impact, implications, or significance |
| **🔗 信息来源** | Source name with hyperlink: `[Source](URL)` |

## Markdown Report Template

```markdown
# [Region]新闻速览(第N期):[Theme/Title]

## [Time Period]资讯汇总

[Opening paragraph: 1-2 sentences summarizing the report scope and key themes]

---

## [Category 1]: [Theme]

### 1. [Event Title]

| 信息 | 内容 |
|:---|:---|
| **📰 事件标题** | ... |
| **📅 发生时间** | ... |
| **🧩 事件概要** | ... |
| **🌍 涉及机构** | ... |
| **🔍 影响或意义** | ... |
| **🔗 信息来源** | [Source](URL) |

---

[Repeat for each event, grouped by category. Within each category, order events chronologically by date]

## 总结

[Concluding analysis highlighting key themes and trends]
```

## HTML Generation

When user requests HTML output, generate a mobile-friendly page with these features:

### Design Elements

- **Theme**: Northern lights / Arctic winter aesthetic (dark blue gradients, cyan/pink accents)
- **Background**: Animated aurora waves + falling snowflakes
- **Typography**: Playfair Display (headings) + Noto Serif SC (body) + Source Sans 3 (UI)
- **Layout**: Card-based, expandable news items with category color coding

### Category Colors

| Category | CSS Class | Color |
|----------|-----------|-------|
| 极端天气 | `weather` | Sky blue (#38bdf8) |
| 医疗系统 | `health` | Emerald (#34d399) |
| 市政服务 | `city` | Purple (#a78bfa) |
| 能源经济 | `energy` | Orange (#fb923c) |
| 教育科研 | `education` | Pink (#f472b6) |
| 公共卫生/环境 | `environment` | Lime (#84cc16) |
| 社区发展 | `community` | Blue (#60a5fa) |

### HTML Structure

```html
<!-- Header -->
<header>
    <span class="issue-badge">Digital Garden 第N期</span>
    <h1>[Region]<br>新闻速览</h1>
    <p class="subtitle">[Time Period] · [Theme]</p>
    <div class="intro">[Intro paragraph]</div>
</header>

<!-- TOC Navigation -->
<nav class="toc">
    <a href="#category-id" class="toc-item [category-class]">🎯 [Category Name]</a>
    ...
</nav>

<!-- News Sections -->
<section class="section" id="[category-id]">
    <div class="section-header">
        <span class="section-icon">[emoji]</span>
        <h2 class="section-title">[Category]: [Theme]</h2>
    </div>
    
    <article class="news-card [category-class]" data-news="[n]">
        <h3 class="news-title">
            <span class="news-number">[n]</span>
            [Headline]
        </h3>
        <div class="news-meta">
            <span class="news-meta-item">📅 [Date]</span>
            <span class="news-meta-item">📍 [Location]</span>
        </div>
        <p class="news-summary">[Summary]</p>
        <div class="news-details">
            <div class="detail-row">
                <div class="detail-label">🌍 涉及机构</div>
                <div class="detail-content">[Organizations]</div>
            </div>
            <div class="detail-row">
                <div class="detail-label">🔍 影响或意义</div>
                <div class="detail-content">[Impact]</div>
            </div>
            <a href="[URL]" target="_blank" class="news-source">🔗 [Source]</a>
        </div>
        <button class="expand-btn" onclick="toggleCard(this)">
            <span>查看详情</span>
            <span class="arrow">▼</span>
        </button>
    </article>
</section>

<!-- Summary Section -->
<section class="summary-section">
    <h2 class="summary-title">📊 本期要点总结</h2>
    <ul class="highlight-list">
        <li class="highlight-item">
            <span class="highlight-number">[n]</span>
            <div class="highlight-content">
                <div class="highlight-title">[Point Title]</div>
                <div class="highlight-desc">[Description]</div>
            </div>
        </li>
        ...
    </ul>
</section>
```

### Key Features

- Expandable news cards (click to show/hide details)
- Smooth scroll navigation via TOC
- Back-to-top floating button
- Print-friendly styles
- Responsive design for all screen sizes

## Writing Guidelines

- Use objective, concise journalistic tone
- Write in the language matching user preference (Chinese or English)
- Group events by thematic categories, and within each category, order events chronologically by date (from oldest to newest)
- Verify all source URLs are functional
- Include separator `---` between events for readability

## References

- For a complete Markdown example, see [reference.md](reference.md)
- For HTML template and styles, see [assets/news-report-template.html](assets/news-report-template.html)
