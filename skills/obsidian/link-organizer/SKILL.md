---
name: link-organizer
description: Use when the user says 整理連結, 分類連結, organize links, 處理 link.md, sort links, or asks to process/categorize collected links. This skill reads links from the inbox file (link.md), categorizes them by domain, generates Traditional Chinese descriptions, and appends them to the appropriate category files.
---

# Link Organizer

Automatically categorize links from the inbox (`link.md`) into domain-specific files with Traditional Chinese descriptions.

## Configuration

- **Link folder:** `/mnt/c/workspace/obsidian/base/link/`
- **Input:** `link.md`
- **Output files:**

| Category | File | Match Rule |
|----------|------|------------|
| GitHub | `github.md` | `github.com` or `github.io` in URL |
| X/Twitter | `x.md` | `x.com` or `twitter.com` in URL |
| Big Tech | `bigtech.md` | Domain contains `anthropic`, `openai`, `google`, `microsoft`, `apple`, `meta`, `amazon`, `nvidia`, or `deepmind` (including subdomains like `anthropic.skilljar.com`) |
| Others | `others.md` | Everything else |

## Procedure

Process all steps automatically — no confirmation needed.

### Step 1 — Read & Parse link.md

Read `/mnt/c/workspace/obsidian/base/link/link.md` and extract all links.

Handle mixed formats:
- `[text](url)` — standard markdown link
- `[url](url)` — URL as both text and href
- `[url](url "title")` — with title attribute
- Bare URLs on their own line

Extract the **URL** and **display text** for each link.

### Step 2 — Categorize by Domain

For each extracted URL, determine the category by checking the domain against the match rules in the table above. Check in order: GitHub → X/Twitter → Big Tech → Others.

### Step 3 — Generate Traditional Chinese Descriptions

For each link, generate a concise one-line Traditional Chinese summary:

1. Run `defuddle parse <url> -p title` and `defuddle parse <url> -p description` to get page metadata
2. If the title/description are insufficient, run `defuddle parse <url> --md` to fetch full content
3. From the metadata/content, write a concise one-line Traditional Chinese description summarizing what the page is about
4. **Fallback:** If defuddle fails (timeout, 404, etc.), infer a description from the link text and URL path

### Step 4 — Read Existing Category Files & Merge

For each category file that will receive new links:

1. Read the existing file content (if the file exists)
2. **Deduplicate by URL** — strip tracking parameters (`?s=`, `&t=`, `utm_*`, etc.) before comparing. Skip links already present in the file.
3. Append new links at the **end** of the existing list

**Formatting rules per category:**

- **GitHub (`github.md`):**
  ```
  - [owner/repo](url)
    中文描述
  ```
  Shorten display text to `owner/repo` format (e.g., `karpathy/agenthub`).
  Use 2-space indent for description.

- **X/Twitter (`x.md`):**
  ```
  - [username](url)
  	中文描述
  ```
  Use the `@username` handle as display text (without the @).
  Use tab indent for description (matching existing file convention).

- **Big Tech (`bigtech.md`):**
  ```
  ## Company Name

  - [display text](url)
    中文描述
  ```
  Group links under `## Company Name` sub-headings.
  If the company heading already exists, append under it.
  If not, create a new heading at the end of the file.
  Use 2-space indent for description.

- **Others (`others.md`):**
  ```
  - [display text](url)
    中文描述
  ```
  Use 2-space indent for description.

### Step 5 — Create File If Not Exists

If a category file doesn't exist yet, create it with frontmatter matching this pattern:

```yaml
---
title: <category title in Chinese>
date: <today's date YYYY-MM-DD>
tags:
  - link
  - <category-specific tags>
---

# <heading>
```

Reference existing files for exact titles and tags:
- `github.md`: title `GitHub 連結收集`, tags `link, github`
- `x.md`: title `X (Twitter) 連結收集`, tags `link, x, twitter`
- `bigtech.md`: title `Big Tech 連結收集`, tags `link, big-tech`
- `others.md`: title `其他連結收集`, tags `link, others`

### Step 6 — Clear link.md

After all links are successfully categorized and written, **clear the contents of link.md** (write an empty string). This keeps it as an inbox for the next batch.

### Step 7 — Print Summary

Output a summary table:

```
連結整理完成！

| 分類 | 新增 | 重複跳過 |
|------|------|----------|
| GitHub | N | N |
| X/Twitter | N | N |
| Big Tech | N | N |
| Others | N | N |

defuddle 失敗: N 個（已用備用描述）
```
