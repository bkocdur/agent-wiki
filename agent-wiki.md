# Agent Wiki

**A structured knowledge base built for AI agents.**
GitHub: https://github.com/bkocdur/agent-wiki-template
Ask Claude to guide you: https://claude.ai/new?q=Guide+me+to+build+this+template%3A+https%3A%2F%2Fgithub.com%2Fbkocdur%2Fagent-wiki-template

---

## What it is

Most teams run on Slack, meetings, and scattered docs. Knowledge lives in people's heads and disappears when threads scroll past. Agent Wiki is a GitHub template that compiles that knowledge into structured articles that an AI agent can read, reference, and update.

The wiki is designed to be:
- Read by agents as context before they do work (briefings, analysis, planning)
- Updated by agents as new decisions are made
- Auditable by humans when they need to check what was decided and when

---

## How it works

```
Your data sources (Slack, meetings, notes)
        |
  Bootstrap prompt (one-time, run with Claude Code)
        |
  Structured wiki (this repo)
        |
  Agent reads wiki at Step 0 before pulling live data
        |
  Agent flags articles that need updating
        |
  Ingest workflow keeps wiki current
```

---

## Token efficiency

The core pattern is state.md as a compressed snapshot layer.

Without state.md: agents fetch index.md plus matching workstream files plus people files at Step 0. On a mature wiki with 10+ workstreams, this costs 30-40K tokens per run before any live data is pulled.

With state.md: agents fetch one ~800-token file. Individual files are only fetched when a specific gap forces it. On the same wiki, Step 0 costs 800-1,500 tokens.

state.md is regenerated at the end of every briefing run. It is always current as of the last agent cycle. It is never manually edited.

---

## Article format

Every article follows the same structure:

```markdown
---
summary: one sentence
status: Active | Stale | Resolved | Monitoring
last_updated: DD.MM.YYYY
---

# Title

Content body: decisions, numbers, current state.

## Backlinks

- [[related/article]]
```

---

## Folder structure

```
agent-wiki-template/
  CLAUDE.md              # Agent schema: rules for reading and writing articles
  BOOTSTRAP.md           # One-time bootstrap prompt: customize and run
  state.md               # Compressed state snapshot: load this at Step 0 (auto-regenerated)
  index.md               # Article catalog with status tags (auto-generated)
  log.md                 # Update log (auto-generated)
  reports/               # Daily briefing reports (DD.MM.YYYY.md per day)
  workstreams/           # What your team works on
  people/                # Who does what, open items, delivery patterns
  experiments/           # Tests and pilots with hypotheses and results
  decisions/             # Chronological decision log
  metrics/               # Definitions and benchmarks
  templates/             # Example articles to reference
```

---

## Quickstart

### 1. Clone this repo

```bash
git clone https://github.com/bkocdur/agent-wiki-template my-wiki
cd my-wiki
rm -rf .git && git init
```

### 2. Configure CLAUDE.md

- Replace the owner block with your name, role, and company
- Replace the Slack channel ID with your briefing or operations channel
- Adjust workstreams and people to match your team

### 3. Run BOOTSTRAP.md

Open Claude Code in your wiki directory and paste the contents of BOOTSTRAP.md. The agent will:

1. Fetch your source messages (Slack, or any connected MCP tool)
2. Create all articles with YAML frontmatter, content, and backlinks
3. Build the index and decision log
4. Generate state.md
5. Commit and push to GitHub

### 4. Add Step 0 to your agent prompts

```
Step 0 - Wiki context: Fetch state.md from your wiki repo. This single file
contains current open items, workstream statuses, active experiments, people
watch list, and key metrics. Use it as your baseline.

Only fetch individual workstream or people files if state.md lacks enough
detail for today's specific task. Fetch minimum necessary.

Regenerate state.md completely after every agent run that produces new
decisions or status changes.
```

---

## Supported data sources

| Source | How to connect |
|---|---|
| Slack | MCP Slack tool (used in bootstrap prompt) |
| Meeting notes | Granola, Otter, Fireflies: paste transcripts or connect via MCP |
| Apple Notes | MCP Apple Notes tool |
| Google Docs | MCP Google Drive tool |
| Email | MCP Gmail tool |
| Notion | MCP Notion tool |
| Linear / Asana | MCP project management tools |
| Manual | Paste raw text into the bootstrap prompt |

---

## Content rules

- Write decisions, not discussions. If something was discussed but not decided, mark it [OPEN].
- Write in present tense for current status, past tense for decisions already made.
- Include numbers wherever they appear in the source (percentages, dollar amounts, KPIs).
- Backlink everything. Every person mentioned gets a link to their people/ article.

## Staleness rule

Articles not updated in 7+ days get a [STALE] tag in index.md. This surfaces when context is aging and may no longer be accurate.

---

## FAQ

**How is this different from a regular wiki?**
Regular wikis are written for humans and go stale. This wiki is written for agents: the format is optimized for machine reading, and the staleness rule forces updates.

**Do I need Slack?**
No. Slack is just the most common source. Any chronological record of decisions works: meeting notes, email threads, daily standups, even a running Google Doc.

**How many articles should I start with?**
Start with 5-10 workstreams, your direct reports as people articles, and any active experiments.

**Can multiple people maintain the wiki?**
Yes. It is a git repo. Multiple people can update articles and the merge process handles conflicts. log.md provides an audit trail.

**Do I need to know how to code?**
No. If you can use GitHub and paste text into Claude, you can set this up. For the bootstrap step, Claude can guide you through every action.

---

## Credits

Created by Baris Kocdur (https://github.com/bkocdur). Built with Claude Code.
MIT License.
