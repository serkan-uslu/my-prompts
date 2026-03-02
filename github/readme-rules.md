# GitHub README Rules

## 🎯 Purpose
System prompts for writing a GitHub README. Three types cover most repos — pick the one that matches, fill in the variables, paste to Claude.

## 📋 Context
A README has one job: make a stranger (or future you) understand what this is and whether it's useful — in under 30 seconds. Every section that doesn't serve that goal is clutter.

---

## Type 1 — Code Repo (App, API, Tool)

```
Write a GitHub README for a code repository. Use this exact structure. Be concise — no filler sentences.

Project: {project_name}
What it does: {one_sentence_description}
Tech stack: {stack}
Target user: {who_uses_it}

**Structure to follow:**

# {project_name}

> {one_sentence_tagline} — what it does, not what it is built with

## What It Does
2-3 sentences. Lead with the problem it solves, not the technology.
If there's a screenshot or demo GIF, put it here.

## Quick Start
Commands only. Goal: user should be running in under 60 seconds.
```bash
# Clone and install
# Configure env
# Run
```

## Requirements
- Node.js 20+, etc.
- Any accounts or API keys needed

## Configuration
Table of environment variables with description and whether required.
| Variable | Description | Required |

## Tech Stack
Bullet list — framework, major libraries. No paragraph.

## License
One line: MIT, Apache, etc.

Rules:
- No "Introduction" or "About" section headings — just write the content
- No badges unless they add information (build status yes, "made with ❤️" no)
- No "Contributing" section unless the repo is actually open for contributions
- No table of contents for READMEs under 400 lines
- Code blocks for every command — never plain text commands
```

---

## Type 2 — Content Repo (Prompts, Notes, Reference, Cheatsheet)

```
Write a GitHub README for a content repository — not a code project, but a curated collection of {content_type}.

Project: {project_name}
What's in it: {what_the_collection_contains}
Who it's for: {target_user}
How they use it: {primary_use_case}
Core philosophy in one sentence: {philosophy}

**Structure to follow:**

# {project_name}

> {tagline} — what problem this collection solves

## What This Is
2-3 sentences. Answer: what's in here, who it's for, and why it exists.
Do NOT explain what prompt engineering is. Assume the reader knows.

## The Problem → The Solution
Short. 2 sentences max per side.
**Problem:** {what_the_reader_currently_suffers_without_this}
**Solution:** {how_this_repo_fixes_it}

## How to Use
Numbered steps — 3 max. Make the first step achievable in under 2 minutes.

## What's Inside
A clean table or tree of the structure. One-line description per section.

## Philosophy
2-4 bullet points. The "why behind the what" — what principles guided the decisions.

Rules:
- No installation section — it's content, not code
- No "Star this repo" begging
- The tagline must describe the outcome for the user, not the repo's category
- Write for someone who found this via Google, not someone who already follows you
```

---

## Type 3 — Boilerplate / Starter Repo

```
Write a GitHub README for a starter template / boilerplate repository.

Project: {project_name}
What it bootstraps: {what_kind_of_project}
Stack: {stack}
Key decisions made: {opinionated_choices}

**Structure to follow:**

# {project_name}

> Start building {what}, not configuring it.

## What's Included
Bullet list of what's already set up — be specific.
Not "great developer experience" — list the actual tools and configs.

## Quick Start
```bash
# clone or use template button
# install
# run dev
```
Target: running in under 2 minutes.

## Stack
Table or list. For each tool: what it is + why it was chosen over alternatives.
| Tool | Purpose | Why This One |

## Structure
File tree of the important parts. Annotate what's where.

## What's NOT Included (Intentionally)
List things people might expect but aren't there, and why.
This prevents "why doesn't this have X" issues.

## Customization
The 3-5 things most people need to change first.

Rules:
- Lead with speed: "start building in 2 minutes" not "a comprehensive boilerplate"
- Be explicit about opinions — "we use X, not Y, because Z"
- List what's excluded — this builds trust
- No "Why I built this" section unless it directly explains a non-obvious choice
```

---

## Which Type for Which Repo

| Repo type | Template to use |
|-----------|----------------|
| SaaS, web app, mobile app | Type 1 — Code |
| REST API, CLI tool | Type 1 — Code |
| npm library / package | Type 1 — Code |
| Prompt collection, cheatsheet, notes | Type 2 — Content |
| Awesome list | Type 2 — Content |
| Starter template, boilerplate | Type 3 — Boilerplate |
| Dotfiles | Type 1 — Code (treat setup as "install") |
| Portfolio site | Type 1 — Code (demo link is the Quick Start) |

---

## Universal README Rules (Apply to All Types)

```
Regardless of repo type, apply these rules to every README:

**Writing**
- First sentence of every section earns the reader's attention or they stop reading
- Present tense: "This does X" — not "This will do X" or "This is designed to do X"
- Specific over vague: "reduces setup from 2 hours to 5 minutes" beats "saves time"
- No filler: "This project aims to..." → delete "aims to", just say what it does

**Structure**
- The most important information comes first — no warming up
- If a section is empty or obvious, delete it — don't keep it "for completeness"
- Headers are navigation, not decoration — only add them if the section is long enough to need one

**Code**
- Every command in a code block — never inline code for commands you run
- Show the minimal working example first, then options
- Don't show output unless it helps understanding

**Formatting**
- One blank line between sections
- Bold for terms, not for emphasis of random words
- Tables for comparisons, bullet lists for options — not paragraphs
```

---

*Last updated: 2026-03-02*
