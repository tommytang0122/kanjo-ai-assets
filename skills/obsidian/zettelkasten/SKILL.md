---
name: zettelkasten
description: "Decompose long Obsidian notes into atomic Zettelkasten notes (one concept per note) and upgrade bare wikilink lists into structured Maps of Content (MOCs). Use this skill whenever the user wants to atomize notes, break down a document, extract ideas from specs or plans into individual notes, split a long note, create atomic notes, upgrade or organize a MOC, or mentions Zettelkasten. Also use when the user has a note with multiple topics they want separated, or a link-heavy note that needs structure and descriptions added."
---

# Zettelkasten Skill

Decompose long notes into atomic notes (one concept each) in the Zettelkasten vault. Upgrade bare wikilink lists into structured Maps of Content (MOCs).

## Vault Configuration

| Vault | Path |
|-------|------|
| **Source** (read from) | `/mnt/c/workspace/obsidian_vault/` |
| **Zettelkasten** (write to) | `/mnt/c/workspace/obsidian/zettelkasten/` |

**Never modify source notes destructively.** Only append a reference list to the source note after atomization.

## Dependencies

- **REQUIRED:** `obsidian-markdown` -- for Obsidian syntax (wikilinks, frontmatter, callouts)
- **REQUIRED:** `obsidian-tommy-style` -- for note formatting conventions and frontmatter style
- **OPTIONAL:** `obsidian-cli` -- for discovery mode (searching vault for related notes)

## Operation 1: Atomize Notes

### When to Atomize

- Note covers multiple distinct concepts (typically >100 lines, but shorter notes with clearly separable ideas qualify too)
- Specs or plans with separable components (architecture, data model, UI, etc.)
- Research notes mixing multiple topics or frameworks

### When NOT to Atomize

- Already atomic (single focused concept)
- Daily logs or journal entries
- Existing MOCs (upgrade instead -- see Operation 2)
- Short notes focused on one idea

### Process

#### Step 1: Analyze Source Note

Read the source note and identify distinct concepts. A "concept" is a self-contained idea that makes sense on its own: a design pattern, a data structure, a workflow, a decision rationale, etc.

#### Step 2: Present Proposed List for Approval

Present the proposed atomic notes to the user as a numbered list with title and one-line description:

```
Proposed atomic notes from「遊戲設計文件.md」:

1. 回合制狀態機設計 -- State machine architecture for turn-based combat
2. 角色屬性系統 -- Character attribute system and stat calculations
3. 技能樹結構 -- Skill tree data structure and progression rules
4. 戰鬥傷害公式 -- Damage calculation formula and modifiers
5. AI 對手決策邏輯 -- Enemy AI decision-making logic

Proceed? (user may add, remove, rename, or merge items)
```

The user may approve all, select a subset, rename items, or merge related concepts. **Do not write any files until the user approves.**

#### Step 3: Create Topic Folder and Write Atomic Notes

Create a folder in the Zettelkasten vault named after the original topic (the source note's title or main subject), then write each atomic note inside it.

```
/mnt/c/workspace/obsidian/zettelkasten/
└── 如何成為世界級 Agentic Engineer/
    ├── Context 精準控制.md
    ├── 研究與實作分離.md
    ├── 討好傾向的設計限制.md
    └── ...
```

Rules for each atomic note:

- Use `obsidian-tommy-style` formatting for frontmatter and structure
- Add wikilinks between related atomic notes (siblings from the same decomposition)
- **Do NOT add backlinks to the source note** -- atomic notes stand alone

#### Step 4: Update Source Note

Append a plain-text reference list to the source note under a new section:

```markdown
## 原子筆記 (Atomic Notes)

以下筆記已提取至 Zettelkasten vault:

- 回合制狀態機設計
- 角色屬性系統
- 技能樹結構
- 戰鬥傷害公式
- AI 對手決策邏輯
```

> **Plain text, NOT wikilinks.** Cross-vault wikilinks (`[[note]]`) do not resolve in Obsidian. Use plain text as a reference list only.

#### Step 5: Cross-link Review

Review all newly created atomic notes and ensure wikilinks between related siblings are present and accurate. Add links where concepts reference each other.

### Atomic Note Quality Criteria

| Criteria | Requirement |
|----------|-------------|
| **Scope** | One concept per note |
| **Self-contained** | Understandable without reading the source |
| **Title** | Descriptive concept name |
| **Length** | 10--80 lines |
| **Frontmatter** | tommy-style properties |
| **Links** | Wikilinks to related sibling atomics |

### Naming Conventions

- Use descriptive concept names: `回合制狀態機設計`, `React Server Components 原理`
- **No ID prefixes** (no `202603051200`)
- **No date prefixes**
- **No source prefixes** (no `遊戲設計文件 - 回合制狀態機設計`)

## Operation 2: Upgrade Proto-MOCs

### Identifying Proto-MOCs

A proto-MOC is a note that is mostly a bare list of wikilinks with little or no context, descriptions, or grouping. Examples: a note that is just 20 `[[links]]` in a row, or a note with a heading and a flat bullet list of links.

### Upgrade Process

1. **Read all linked notes** to understand what each link actually covers
2. **Group links by theme** into logical sections
3. **Add section headings** with brief descriptions
4. **Add one-line descriptions** after each link explaining its content
5. **Add frontmatter** using tommy-style if missing

### Example

**Before** (proto-MOC):
```markdown
# Agentic AI

[[AutoGPT]]
[[BabyAGI]]
[[ReAct Pattern]]
[[Tool Use]]
[[Chain of Thought]]
[[LangChain Agents]]
[[CrewAI]]
[[Function Calling]]
[[Reflection Pattern]]
[[Planning Patterns]]
```

**After** (structured MOC):
```markdown
---
title: Agentic AI
tags:
  - MOC
  - AI
---

# Agentic AI

自主 AI 代理的架構模式、框架與推理策略。

## 推理模式 (Reasoning Patterns)

- [[Chain of Thought]] -- 逐步推理提升複雜任務表現
- [[ReAct Pattern]] -- 結合推理與行動的交替迴圈
- [[Reflection Pattern]] -- 自我評估與修正輸出品質
- [[Planning Patterns]] -- 任務分解與執行策略

## 工具與執行 (Tool Use & Execution)

- [[Tool Use]] -- 代理呼叫外部工具的機制
- [[Function Calling]] -- LLM 結構化函式呼叫介面

## 框架 (Frameworks)

- [[LangChain Agents]] -- LangChain 的代理抽象層
- [[CrewAI]] -- 多代理協作框架
- [[AutoGPT]] -- 早期自主代理實驗
- [[BabyAGI]] -- 任務驅動的自主代理原型
```

## Discovery Mode

**Default:** Process file or folder paths provided by the user directly.

**Optional enhanced mode:** When Obsidian is running and `obsidian-cli` is available, use `obsidian search` to find related notes across the vault. This helps:

- Suggest additional wikilinks to existing notes in the Zettelkasten vault
- Find duplicate or overlapping concepts before creating new atomic notes
- Discover related MOCs that should link to new atomic notes

Only use discovery mode when the user requests it or when richer linking would clearly benefit the output.

## Verification Checklist

Before reporting completion, verify:

- [ ] Each atomic note covers exactly one concept
- [ ] Each atomic note has tommy-style frontmatter
- [ ] Each atomic note is self-contained (readable without the source)
- [ ] Atomic notes use wikilinks to related siblings
- [ ] Topic folder created in `/mnt/c/workspace/obsidian/zettelkasten/<topic>/`
- [ ] All atomic notes written inside the topic folder
- [ ] Source note updated with plain-text list under `## 原子筆記 (Atomic Notes)`
- [ ] MOC upgrades have thematic groupings and descriptions
- [ ] No cross-vault wikilinks used in source note updates
