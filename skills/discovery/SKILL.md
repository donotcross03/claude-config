---
name: discovery
description: >
  Run structured discovery cycles for public safety software sales and customer conversations.
  Use this skill whenever Vlad is preparing for a discovery call, customer scoping meeting,
  sales conversation, or any pre-engagement where the goal is to understand a problem before
  proposing a solution. Trigger on phrases like "discovery prep", "prep for the call",
  "what should I ask", "help me frame the problem", "derisking the conversation",
  "questions for [customer]", or any meeting prep that centers on understanding customer
  scenarios, systems, stakeholders, or timelines. Also trigger when Vlad pastes call notes
  or a transcript and says "apply", "capture this", "process this", or "synthesize what we
  learned." Four commands drive the workflow: /discovery prep, /discovery capture,
  /discovery synthesize, /discovery brief. Always use this skill — do not attempt
  discovery prep from scratch.
---

# CAD Discovery Skill

Runs a structured, hypothesis-driven discovery cycle across four commands. Output is a
single living `.md` document per engagement — created by `/discovery prep`, updated by
`/discovery capture`, finalized by `/discovery synthesize`, exported by `/discovery brief`.

The core discipline: commit to a hypothesis before the call. Use questions to stress-test
it, not confirm it. The document is the audit trail of how the problem understanding evolved.

---

## Bare Invocation — `/discovery` with no arguments

If the user invokes `/discovery` without a subcommand, respond with:

> Which command do you need?
> - **`prep`** — prepare for an upcoming call (cold: paste context, or warm: paste existing discovery doc)
> - **`capture`** — log notes or a transcript after a call
> - **`synthesize`** — finalize findings when enough is known to commit to a problem statement
> - **`brief`** — generate a shareable stakeholder brief (.md + .docx) from the discovery doc

Then wait for the user's choice before proceeding.

---

## Commands

---

### `/discovery prep [context or existing doc]`

Handles two modes. Detect which applies from the input:

**Mode A — Cold prep (no prior calls)**
Input: 2–5 sentences describing the upcoming meeting. Customer name, topic, known context,
who's in the room, any signals already received. Can be rough.

Output: A new discovery document with Hypothesis and Questions sections populated from scratch.

**Mode B — Warm prep (prior calls already captured)**
Input: The existing discovery document (paste it). User wants prep for the NEXT call,
informed by what has already been captured and confirmed/denied.

Output: The existing document updated with:
- A revised or sharpened `## Questions` section built specifically for the next call
- Each question annotated with which open hypothesis assumption or risk it targets
- A `_Prep note:_` at the top of the Questions section summarizing what's already confirmed
  (skip re-asking) and what's still open (must ask)
- No changes to the Capture Log or existing Hypothesis block — preserve the audit trail

**Prep Rules (both modes):**
- Write or refine the hypothesis before generating questions. Questions must serve the hypothesis.
- If context is thin, make assumptions explicit and flagged.
- Do not generate generic questions. Every question must connect to a specific assumption.
- Flag any assumption that, if wrong, would invalidate the entire solution direction.
- In warm mode: do not re-ask questions already answered. Focus on what's open or contradicted.
- Public safety domain defaults: assume P1 or CC CAD unless stated otherwise. Assume
  multi-agency if county or metro. Assume CJIS sensitivity for anything touching identity
  or query data. Assume dispatcher workload is the primary constraint.

---

#### Document Structure (created by cold prep)

```
# Discovery — [Customer] — [Topic]
_Created: [date] | Status: In Progress_

---

## Hypothesis

**Problem (assumed):**
What we think is actually happening operationally — not the surface request.
One to three sentences. Specific. Falsifiable.

**Solution Direction (assumed):**
What we'd likely build or recommend if the hypothesis is correct.
One to two sentences. Not a commitment.

**Customer Outcome if Resolved:**
What changes for dispatchers, supervisors, or the agency if this is solved.
Concrete. Tied to workflow, not features.

**Draft Success Criteria:**
What would we need to see to call this solved? Two to four measurable conditions.

---

## Questions

_Prep note: [what's already confirmed / what must still be asked — used in warm mode]_

Questions are organized to validate or invalidate the hypothesis — not to sell.
Sequence: scenario first, systems second, stakeholders third, timeline/budget last.

### Scenario & Workflow
> Goal: understand what's actually happening operationally before proposing anything.

- [3–5 questions that surface the real problem scenario]

### Current Systems & Environment
> Goal: understand integration surface and constraints before scoping.

- [3–4 questions about current CAD, radio, provisioning, integrations]

### Stakeholders
> Goal: map who owns the problem, who blocks the solution, who defines success.

- [2–3 questions about decision makers, operators, IT, and champions]

### Timeline & Budget
> Goal: qualify the opportunity without leading with price.

- [2–3 questions that surface urgency, funding signals, and decision process]

### Risk Signals
> Goal: identify what would kill this before it starts.

- [2–3 questions about past attempts, blockers, political constraints]

---

## Capture Log

_Populated after each call via /discovery capture_

---

## Open Risks

_Maintained throughout. Items that are unresolved and could derisk or kill the engagement._

---

## Summary

_Populated by /discovery synthesize_
```

---

### `/discovery capture [notes | transcript]`

**Aliases:** "apply", "process this", "capture this transcript", "log this call"
If the user pastes a transcript or notes and says any of the above, treat it as `/discovery capture`.

**Input:**
- Raw call notes, bullet fragments, or a full Gemini/AI transcript — format does not matter
- If an existing discovery document exists for this engagement, the user should paste it first,
  then the notes. If no prior doc exists, create one and capture directly into it.

**Before logging, determine call type.** Infer from context or ask:
- `[Customer]` — the customer/agency was on the call (decision-makers, operators)
- `[Pre-sales/Partner]` — sales rep, solutions partner, or channel contact (not end customer)
- `[Internal]` — MSI internal alignment only (no customer or partner present)

Label the capture entry accordingly: `### Call [N] — [date] [type]`

**Output:** Updated discovery document with:
- A new dated and typed `### Call [N] — [date] [Customer | Pre-sales/Partner | Internal]` section
  appended to the Capture Log
- Each capture section maps to the question categories: Scenario, Systems, Stakeholders,
  Timeline, Risk
- Each hypothesis assumption marked: `✓ Confirmed` / `✗ Invalidated` / `~ Partially` /
  `? Still open`
- Open Risks updated — new risks added, resolved ones closed with note
- Hypothesis block annotated inline with delta markers where reality diverged

**Capture Rules:**
- Do not rewrite the original hypothesis — annotate it. The audit trail matters.
- If a critical assumption was invalidated, flag it prominently:
  `⚠ HYPOTHESIS SHIFT: [what changed]`
- If the call produced a new problem framing that contradicts the original, capture both
  and leave resolution to `/discovery synthesize`
- Preserve the customer's language where possible — do not translate into product terms yet
- For internal or pre-sales calls: still capture what was learned about the customer's
  environment, constraints, and signals. These calls inform the hypothesis even without
  the customer present.

---

### `/discovery synthesize`

**Input:** The full discovery document (paste it in). Run after all calls for an engagement
are complete, or when enough is known to commit to a problem statement.

**Output:** Updated document with the Summary section fully written:

```
## Summary

**Refined Problem Statement:**
What the customer's actual operational problem is, in plain language.
Not what they asked for. Not our product framing. The real thing.

**Validated Solution Direction:**
What we'd recommend building or scoping, and why it fits the problem.
What we explicitly would NOT build and why.

**Confirmed Assumptions:**
[List from hypothesis that held]

**Invalidated Assumptions:**
[List from hypothesis that didn't hold, with what replaced them]

**Open Risks:**
[Anything still unresolved that should block a proposal or scope commitment]

**Recommended Next Step:**
One specific action — another call, a demo, a scope doc, a pilot proposal.
Who owns it, and by when if known.
```

**Synthesis Rules:**
- The refined problem statement must be writable without using any product name.
  If you can't describe the problem without naming CommandCentral or RaTX, the
  problem isn't defined yet.
- Do not synthesize if more than two critical assumptions are still open — flag this
  and recommend another capture call instead.
- The summary should be shareable with sales or engineering as a standalone artifact.
  No internal shorthand. No assumed context.

---

### `/discovery brief`

**Aliases:** "output this into a doc", "make a brief", "export this", "turn this into a brief"
If the user asks to export the discovery findings into a shareable format, treat as `/discovery brief`.

**Input:** The full discovery document (paste it in), or reference to an existing engagement
if already in context.

**Output:** Two files for the engagement:
1. `~/Documents/Claude_code/briefs/brief-[customer]-[topic].md` — structured stakeholder brief
2. `~/Documents/Claude_code/briefs/brief-[customer]-[topic].docx` — formatted Word document
   (generated via python-docx; use consistent styling: blue headers, color-coded phase blocks,
   callout boxes for risks, tables for agency/solution comparisons)

**Brief structure (8 sections):**

```
1. Problem Description & Context
   Operational problem in plain language. Three scenarios or use cases if applicable.
   Include prior failed attempts if known — this builds credibility.

2. Known Agencies & Primary Champion
   Table: Agency | Role | CAD | Status
   MSI team members and their roles.
   Callout: who the decision-maker is and whether they've been identified.

3. Why Now
   What changed that makes this viable at this moment.
   Funding, infrastructure, policy, prior precedent.

4. What Does Success Look Like
   Three tiers: minimum viable (pilot), intermediate, full outcome.
   Explicit "not success" section — what we will not build or commit to.

5. Hypothesis of Solution & Its Evolution
   The core distinction table (e.g., CAD-to-CAD vs. common CAD).
   Crawl → Walk → Run blocks, color-coded, with what each delivers and does NOT deliver.

6. What to Start With
   Specific recommended starting point with rationale.
   Integration starting points ranked by complexity.
   Explicit "do not start with" list.

7. Top Questions
   The 6–8 sharpest questions for the next customer call.
   Lead with the single most important question.
   For each question, note why it matters if non-obvious.

8. Envisioned System Schematic
   ASCII diagram showing: current state, crawl state, walk state, run state.
   "What this is NOT" block at the bottom.
```

**Brief Rules:**
- The brief is audience-facing (sales, engineering, customer). No internal shorthand.
- The schematic must show current state first — the "no solution" picture grounds everything.
- The "not success" and "do not start with" sections are load-bearing. Include them.
- For .docx: use python-docx. Blue (#0060A0) section headers. Alternating row tables.
  Color-coded crawl/walk/run blocks (blue/green/purple tints). Callout boxes (yellow tint)
  for warnings and critical risks. Monospace font for schematic blocks.

---

## Domain Defaults (Public Safety)

Apply these unless the customer context contradicts them:

| Assumption | Default |
|---|---|
| CAD platform | PremierOne or CommandCentral CAD |
| Deployment | On-prem P1 + cloud CC CAD hybrid |
| Agency structure | Multi-discipline (PD, FD, EMS) — often multi-agency |
| Radio | LMR, Motorola infrastructure, talk-group based |
| Compliance | CJIS-sensitive — any identity or query data |
| Primary user | Dispatcher — high workload, low tolerance for friction |
| Decision maker | Typically IT director + dispatch supervisor jointly |
| Budget signal | Often tied to contract renewal or grant cycle |

---

## Anti-Patterns to Avoid

- **Leading questions:** "You'd probably want transcription, right?" — Never.
- **Solution-first framing:** Describing a feature before the problem is confirmed.
- **Generic questions:** "What are your pain points?" without grounding in scenario.
- **Skipping stakeholder mapping:** Proposals fail when the champion isn't identified.
- **Treating the stated request as the problem:** "We want transcription" is not a problem statement.
- **Re-asking confirmed questions in warm prep:** Check the capture log before generating questions.
- **Treating all calls equally in capture:** Internal and pre-sales calls still contain signal —
  log them, tag them correctly, and use them to sharpen the hypothesis.

---

## Output Format

- Discovery document: Markdown `.md`
  Save to `~/Documents/Claude_code/discovery/discovery-[customer]-[topic].md`
- Brief: `.md` + `.docx`
  Save to `~/Documents/Claude_code/briefs/brief-[customer]-[topic].md` and `.docx`
- Use consistent filenames across all commands for the same engagement
- Present with one-line status after every command: current phase + top open risk
