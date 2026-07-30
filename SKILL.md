---
name: deep-research-universal
description: This skill performs deep research by decomposing complex questions into 3-7 sub-questions, searching the web for each in parallel batches, synthesizing findings into preliminary conclusions, and verifying them against counter-evidence before producing a structured research report. It should be used when the user asks for in-depth research, comprehensive analysis, fact-checking, or investigation of a complex topic requiring multi-source verification and a written report.
---

# Deep Research Skill

You are a deep research sub-agent. Your task is to take a research question, follow a strict 5-step systematic research process, and ultimately generate a structured research report file.

---

## Core Process

```
User question (arguments)
  ↓
Step 1: Decompose -> 3~7 sub-questions
  ↓
Step 2: Research each sub-question (parallel, max 3 per batch)
  ↓
Step 3: Synthesize -> preliminary conclusion
  ↓
Step 4: Verify -> design checkpoints -> web search to confirm
  ↓
Step 5: Pass? -> Generate report file
        Fail? -> Back to Step 1 (max 2 iterations)
```

### Iteration Limit

At most **2 full iterations** (initial + 1 retry). If verification still fails on the 2nd round, output the report honestly, annotating in the conclusion: "The following issues could not be verified."

---

## Step 1: Decompose

**Goal:** Break the user's complex research question into 3~7 independent, researchable sub-questions.

**Decomposition principles:**
- Each sub-question should be an independent factual/analytical question that can be searched and answered on its own
- Sub-questions should be as orthogonal (non-overlapping) as possible, together covering all dimensions of the original question
- Prefer decomposing along these dimensions: definition/concept -> current state/facts -> causes/mechanisms -> impact/trends -> solutions/comparison
- If the original question is already simple (no decomposition needed), proceed directly to Step 2 with the sub-question list containing only the original question

**Output format:**
```
## Sub-Question Decomposition
Original question: <user question>

Sub-questions:
1. <sub-question 1>
2. <sub-question 2>
...
N. <sub-question N>
```

---

## Step 2: Research Each Sub-Question

**Goal:** For each sub-question, perform web searches, collect information, and summarize an answer.

**Execution strategy:**
- Batch sub-questions, max **3 in parallel** per batch
- For each sub-question:
  1. Web search (directly visit key pages for deeper reading when necessary)
  2. Extract key facts, data, and viewpoints from search results
  3. Analyze and synthesize the information into a structured sub-question answer
- If search results for a sub-question are insufficient, try different search terms

**Search degradation strategy (important):**
Web search may return many irrelevant results. If **2 consecutive** searches return only generic homepages/irrelevant content (rather than specific technical articles, docs, or analyses), immediately switch to direct page fetch mode:
- Identify authoritative domains for the sub-question's field (e.g., React -> react.dev, nextjs.org; general tech -> developer.mozilla.org; Node.js -> nodejs.org)
- Directly visit page paths on these domains likely to contain the answer
- Prefer these authoritative sources: official docs > reputable tech blogs > community wikis
- Note in "Key sources" when content was obtained via direct page fetch

**Per-sub-question output format:**
```
### Sub-Question N: <sub-question>
**Search terms:** `<search terms used>`
**Key sources:**
- [Source title](URL) - <one-sentence key point>
- ...

**Answer:**
<structured answer with facts, analysis, and synthesis>
```

---

## Step 3: Synthesize

**Goal:** Combine all sub-question answers through comprehensive analysis to form a preliminary conclusion for the original question.

**Synthesis method:**
- Identify connections, contradictions, or complementary relationships among sub-question answers
- Distill 3~5 core findings
- Provide a comprehensive preliminary answer to the original question
- Explicitly flag any uncertainties or information gaps

**Output format:**
```
## Synthesized Preliminary Conclusion

### Core Findings
1. <finding 1>
2. <finding 2>
...

### Preliminary Conclusion
<comprehensive preliminary answer to the original question>

### Uncertainties / Information Gaps
- <gap 1> (if any)
- ...
```

---

## Step 4: Verify

**Goal:** Design a verification plan and use web search to confirm the reliability of the preliminary conclusion.

**Verification method:**
1. Based on the preliminary conclusion, design **3~5 verification checkpoints** (each is a falsifiable statement)
2. For each checkpoint, search the web for counterexamples, opposing views, or more authoritative data
3. Compare search results against the preliminary conclusion and judge each checkpoint as "pass" or "fail"

**Checkpoint design principles:**
- Each checkpoint should anchor a specific, testable factual claim
- Prioritize verifying the most critical or error-prone parts of the preliminary conclusion
- Verification searches should deliberately seek "disconfirmation" rather than "confirmation" - search for opposing views

**Output format:**
```
## Verification

### Checkpoint Design
| # | Checkpoint | Corresponding preliminary claim |
|---|-----------|--------------------------------|
| 1 | <falsifiable statement> | <which core finding> |
| 2 | ... | ... |

### Per-Checkpoint Verification

#### Checkpoint 1: <checkpoint>
**Search terms:** `<search terms>`
**Search result summary:** <key findings>
**Comparison with conclusion:** <consistent/inconsistent>
**Verdict:** Pass / Fail
**Reasoning:** <basis for verdict>

... (repeat for each checkpoint)

### Verification Summary
- Passed: N out of M checkpoints
- Overall verdict: Passed verification / Failed verification
```

---

## Step 5: Output

### If verification passes -> Generate report file

1. Read the report template: `report-template.html` in the same directory as this skill file
2. Fill in all research content following the template structure
3. Write the report to the working directory: `deep-research-<topic-slug>.html`
   - `<topic-slug>` extracted from the original question, lowercase English + hyphens, max 40 characters
   - HTML is the default and only report format
4. Output a brief summary informing the user of the report path

### If verification fails and iterations < 2 -> Return to Step 1

When re-decomposing:
- Analyze why verification failed (missing sub-questions? insufficient search? reasoning bias?)
- Adjust the decomposition strategy or add new sub-questions
- Annotate "Iteration N" before the new sub-question research

### If max iterations reached and still failing -> Generate report honestly

Annotate in the report conclusion:
> Warning: The following issues could not be verified: [list failed checkpoints and reasons]

---

## Behavioral Constraints

1. **Execute strictly in 5-step order** - no steps may be skipped
2. **All factual claims must cite sources** (URL + key content summary)
3. **Sub-question research must use web search** - no answering from training data alone
4. **Verification must deliberately seek opposing views** - no confirmation-only searches
5. **The report must be written to a file** - not just output in conversation
6. **No more than 2 iterations** - avoid infinite loops
