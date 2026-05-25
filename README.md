# Deep Research

> Decompose complex research questions into sub-questions, research each with live web search, synthesize findings, verify conclusions, and output a structured research report.

---

## Overview

**Deep Research** is a Reasonix skill that transforms any complex research question into a structured, verifiable research report. It follows a rigorous 5-step methodology: **decompose → research → synthesize → verify → output**.

Unlike a simple "ask AI and get an answer" workflow, Deep Research:

- **Decomposes** the question into 3–7 independent sub-questions
- **Researches** each sub-question with real-time web search (up to 3 in parallel)
- **Synthesizes** findings into core discoveries and a preliminary conclusion
- **Verifies** the conclusion by deliberately searching for counter-evidence
- **Iterates** up to 2 rounds if verification fails
- **Outputs** a structured markdown report with full source citations

## How It Works

```
User Question
     ↓
Step 1: Decompose → 3~7 sub-questions
     ↓
Step 2: Research each sub-question (parallel, max 3 per batch)
     ↓
Step 3: Synthesize → preliminary conclusion
     ↓
Step 4: Verify → design falsifiable checkpoints → search for counter-evidence
     ↓
Step 5: Pass? → Generate report file
        Fail? → Back to Step 1 (max 2 iterations)
```

### Step 1 — Decompose

Break the question into 3–7 orthogonal sub-questions following this progression: definition/concept → current state/facts → causes/mechanisms → impact/trends → solutions/comparisons.

### Step 2 — Research

For each sub-question, run web searches and optionally `web_fetch` key pages. Extract facts, data, and viewpoints. Every sub-question gets a structured answer with cited sources.

**Search degradation strategy:** If 2 consecutive searches return only irrelevant homepage results, fall back to direct `web_fetch` on authoritative domains (official docs > reputable blogs > community wikis).

### Step 3 — Synthesize

Cross-reference all sub-question answers. Identify 3–5 core findings. Form a preliminary conclusion. Flag uncertainties and information gaps explicitly.

### Step 4 — Verify

Design 3–5 **falsifiable** verification checkpoints — claims that *can* be proven wrong. Search for counter-evidence, opposing views, or more authoritative data. Each checkpoint gets ✅ Pass or ❌ Fail with reasoning.

### Step 5 — Output

| Scenario | Action |
|----------|--------|
| Verification passes | Generate structured report file (`deep-research-<topic-slug>.md`) |
| Verification fails, iterations < 2 | Return to Step 1 with adjusted decomposition |
| Max iterations reached, still failing | Generate report with ⚠️ annotations on unresolved issues |

## Report Structure

Generated reports follow a standardized 6-section format with full source traceability:

| Section | Content |
|---------|---------|
| 1. Sub-Question Breakdown | Decomposition table |
| 2. Per-Question Research | Search results, sources, and structured answers for each sub-question |
| 3. Preliminary Synthesis | 3–5 core findings, preliminary conclusion, information gaps |
| 4. Verification | Falsifiable checkpoints, counter-evidence search, per-checkpoint pass/fail |
| 5. Final Conclusion | Revised answer after verification (or annotated with unresolved issues) |
| 6. Source Index | All cited sources with URLs |

## File Structure

```
deep-research/
├── SKILL.md                # Skill definition
├── report-template.md      # Report output template
├── README.md               # This file
└── README_CN.md            # Chinese readme
```

## Installation

1. Copy the `deep-research/` directory into your Reasonix (or Claude Code) skills folder:
   - **Project-scoped:** `<project>/.reasonix/skills/deep-research/`
   - **Global:** `~/.reasonix/skills/deep-research/`

2. The skill will appear in your Skills index on the next session launch (or `/new`).

## Usage

Invoke via the skill system:

```
/deep-research What are the latest advancements in solid-state batteries?
```

Or programmatically:

```
/run-skill deep-research "Compare Rust vs Zig for systems programming in 2025"
```

The skill spawns as a subagent, runs the full research pipeline independently, and returns the path to the generated report file.

## Requirements

| Requirement | Detail |
|-------------|--------|
| **Platform** | Claude Code, Reasonix (or compatible agent runtime) |
| **Tools** | `web_search`, `web_fetch`, `write_file`, `read_file` |
| **Model** | Recommended: `deepseek-v4-pro`; minimum: `deepseek-v4-flash` |

## Design Principles

| Principle | How It's Applied |
|-----------|-----------------|
| **Decompose before research** | Prevents shallow answers to complex questions |
| **Every claim needs a source** | All factual assertions must cite URLs |
| **Verify by seeking disconfirmation** | Actively search for counter-evidence, not just confirmation |
| **Iterate, don't fabricate** | Up to 2 rounds; failures are annotated, never invented |
| **Structured output** | Consistent report format enables cross-topic comparison |


