---
name: e2e-flow
description: |
  Maps any system, integration, or business process into a clear end-to-end flow from raw inputs like meeting
  notes, Slack threads, rough docs, architecture descriptions, or verbal explanations.

  Use whenever someone wants to understand or document how something works end-to-end: which systems are involved,
  who owns what, what triggers what, what data moves where, and where the gaps in understanding are.

  Trigger on: "map out this flow", "help me understand how this works", "document this process", "what systems
  are involved", "create a flow diagram", "draw out the architecture", "clarify how X connects to Y", "I have
  notes about our integration", "what are the open questions", "let me paste our docs / notes / transcript".
  Also trigger when the user dumps raw notes or a description and asks for structure, clarity, a diagram, or
  "what's missing". Trigger even if the request is casual — use this any time someone is trying to get their
  head around a system or process.
---

# E2E Flow Mapper

You're going to take whatever the user gives you — notes, docs, a transcript, a brain dump — and turn it into
a clear, structured picture of how something works end-to-end. Your job is part investigator (find what's there),
part architect (structure it clearly), and part skeptic (name what's missing or assumed).

## What to do

Work through these steps and produce a single structured output. Don't ask clarifying questions first — dig into
whatever is given and surface questions in the output itself. That's more useful than a pre-interview.

**Output format:** produce the full structured analysis as a response AND save it as a markdown file to
`~/Downloads/{slug}-e2e-flow.md` where `{slug}` is a short kebab-case name derived from the flow topic
(e.g., `device-id-provisioning`, `order-payment-flow`). Also generate and save SVG diagram files alongside
the markdown. At the end, confirm the files saved with their paths.

---

### Step 1: Gather the raw material

Read everything the user provided carefully. If they mentioned files or shared a doc, read those too. Look for:
- Named systems, services, APIs, databases, queues, UIs
- Named people, teams, roles
- Actions and verbs (sends, triggers, stores, validates, notifies...)
- Data: what gets passed, what format, what direction
- Time/sequence cues (first, then, after, when, once...)
- Ownership hints (team names, "owned by", contact names)

Derive a `{slug}` from the flow topic before producing output. Use it for all saved filenames.

---

### Step 2: Produce the output

Use the exact sections below, in order. Omit a section only if it's genuinely not applicable (e.g., no owners
mentioned at all and none are inferable). Don't pad sections with filler.

---

## Section 1 — Systems & Actors

A table of every named system, service, API, database, team, or role involved.

| Name | Type | Role in This Flow | Owns / Responsible For |
|------|------|-------------------|------------------------|
| ... | (service / team / person / DB / queue / UI / external) | ... | ... |

Include even things mentioned in passing — a "notification email" implies an email service. Mark things that
are inferred (not explicitly named) with *(inferred)*.

---

## Section 2 — E2E Flow Narrative

A numbered, step-by-step walk-through of the flow from start to finish. Use active voice with a clear subject:
**[Who/What]** → **[does what]** → **[to/from whom]** → **[producing what result/state change]**

Number every step. Show branching with sub-steps (e.g., 3a / 3b). Show loops and retries where they exist or
are implied.

If the start trigger or end condition isn't clear, say so explicitly at the top of this section before the steps.

**Tag each step or phase as new or existing where this is known or inferable:**
- `[EXISTS]` — this step/component already exists and is in use today
- `[EXISTS — DISABLED]` — built and previously worked, but currently turned off or commented out
- `[NEW]` — this step/component needs to be built
- `[NEW — PHASE 2]` — identified but explicitly deferred or out of current scope
- `[UNKNOWN]` — can't determine from the input whether this exists or is new

Place the tag inline at the start of the step or as a block label before a group of steps that share the same
status. If the entire flow is clearly new or clearly existing, note it once at the top rather than tagging
every step. Only tag what is actually known or reasonably inferable — don't guess.

---

## Section 3 — Flow Diagrams

### 3a — Mermaid Diagrams (inline, portable)

Before writing any diagrams, assess the input and decide which types to generate. Each diagram must cover
**one flow at a time** — never combine multiple distinct flows or phases into a single diagram. If the input
has multiple distinct flows (e.g., provisioning vs. login, or admin path vs. user path), produce a separate
diagram for each.

**Use a Sequence diagram** (`sequenceDiagram`) when:
- Three or more distinct systems exchange messages, API calls, or events
- The flow is primarily request/response, async messaging, or a protocol interaction
- Ordering and timing between systems is what needs to be understood

**Use an Activity / Flowchart diagram** (`flowchart TD`) when:
- The flow is linear with decision branches (success/failure, if/else paths)
- It's a user journey or a process with clear sequential stages and at most one or two systems
- You want to show decision logic, state transitions, or conditional paths step-by-step

**Use a Use Case diagram** (`graph LR` — actors left, rounded use-case nodes right) when:
- The input describes multiple distinct scenarios, user roles, or entry points into a system
- You need an orientation map showing "what is possible" before drilling into individual flows

**Rules:**
- One flow per diagram. If the input has N distinct flows, produce N sequence/activity diagrams.
- You may combine types: e.g., a Use Case diagram as an orientation map + one Sequence diagram per scenario.
- Label every diagram clearly: `### [Flow Name] — [Diagram Type]` (e.g., `### User Creation — Sequence Diagram`)
- One sentence below each heading explaining why this type was chosen.

Wrap each in a fenced code block: ` ```mermaid `

Keep diagrams readable — don't cram every detail in. The narrative in Section 2 carries the detail.

### 3b — SVG Diagrams (generated and saved as files)

Generate and save SVG diagram files:

**File 1: `{slug}-overview.svg`** — System Architecture Overview (box diagram)
- Show all systems/actors as labeled boxes, color-coded by type
- Group local components inside a dashed workstation/boundary box where applicable
- Draw directed arrows between systems, labeled with the action or data being passed
- Use color coding: purple = human actors, blue = remote/cloud systems, green = local services/agents,
  amber/brown = local files/storage, teal = web clients, orange = mobile/smart devices, dark blue = backend APIs
- Add a layer label (e.g., "REMOTE / CLOUD", "WORKSTATION (local)") for each tier
- Include a legend and an owner callout bar at the bottom
- Include gap/warning annotations for known issues (use amber background with ⚠ icon)
- Canvas: 1100×660 typical; adjust to fit content

**File 2+: `{slug}-[flow-name].svg`** — Sequence or Activity Diagram (visual, styled), one file per flow
- Each distinct flow gets its own SVG. Name files descriptively: `{slug}-provisioning.svg`,
  `{slug}-login.svg`, `{slug}-usecase.svg`, etc. — not generic `-sequence-1`, `-sequence-2`.
- Each SVG is a styled diagram with:
  - Colored actor boxes at top and bottom (same color scheme as overview)
  - Dashed vertical lifelines
  - Numbered step circles on the far-left column
  - Solid arrows (dark blue #1E3A6E) for requests/actions; dashed arrows (gray #94A3B8) for returns/responses
  - Arrow markers using SVG `<marker>` with `orient="auto"` for correct directionality
  - Message labels above each arrow, sized to fit the span
  - Phase section labels (colored badge, e.g., "SETUP / ADMIN FLOW")
  - Warning annotations (amber rect + ⚠ text) for gap items directly on the relevant step
  - Title bar (dark navy #1B2B5E) across the top
  - Light gray-blue background (#F0F4FB)
  - Footer legend: solid line = request, dashed line = return
  - Canvas: 1000–1100px wide; height scales to number of steps (approx 60px per step + 200px for headers/footers)

**SVG technical requirements:**
- Valid XML SVG with `xmlns="http://www.w3.org/2000/svg"` and explicit `width`/`height`
- Define `<marker>` elements in `<defs>` for arrowheads; use `orient="auto"` so arrows work in both directions
- Use `font-family="'Segoe UI', system-ui, Arial, sans-serif"`
- All text must be explicit `<text>` elements (no foreignObject)
- Escape XML special chars: `&` → `&amp;`, `>` → `&gt;`, `<` → `&lt;`
- **Never use HTML named entities** (`&rarr;`, `&mdash;`, `&laquo;`, etc.) — SVG is XML and only supports `&amp;`, `&lt;`, `&gt;`, `&quot;`, `&apos;`. Use plain Unicode characters directly instead (→ — « »)
- Unicode symbols (⚠ ✓ → —) are fine inside `<text>` elements

After generating both SVGs, reference them in the markdown with relative links:
```markdown
![Overview](./slug-overview.svg)
![Sequence](./slug-sequence.svg)
```

---

## Section 4 — Gaps & Implicit Assumptions

This is where you name what's unclear, missing, or assumed. Use two prefixes:

- **[GAP]** — something that's needed to understand the flow but wasn't provided and can't be inferred
- **[IMPLICIT]** — something that's assumed or implied but never stated explicitly

Be specific about why each item matters. Vague gaps ("unclear how auth works") are less useful than precise
ones ("unclear whether the auth token is passed as a cookie or header — this affects whether the mobile app
and web app can share the same auth implementation").

Only list things that actually matter for building, operating, or understanding the system. Skip pedantic or
trivially answerable items.

---

## Section 5 — Open Questions

A numbered list of questions that need real answers before this flow can be considered well-understood. Each
question should have a brief note on **why it matters** (what decision or action it unblocks).

Focus on questions where the answer would change the design or implementation — not just fill in details.

---

## Section 6 — Next Steps

Concrete, actionable steps someone could take right now to move this forward. Order them by priority — blocking
items first.

Each step should be specific enough to actually do: not "clarify the auth flow" but "schedule a 30-minute call
with the Platform team to walk through how tokens are issued and refreshed in the mobile app".

Include steps for:
- Filling critical knowledge gaps (who to ask, what to ask)
- Decisions that need to be made
- Prototypes or spikes worth doing to validate assumptions
- Documentation or diagrams worth creating

---

## Section 7 — Owners & Contacts

If any ownership or contact information is mentioned or inferable, list it here:

| System / Area | Owner / Contact | How Identified |
|--------------|----------------|----------------|
| ... | ... | (named explicitly / inferred from team name / inferred from system name) |

If nothing is inferable, omit this section.

---

## Saving the output

After all sections are complete:

1. Assemble the full markdown document (all 7 sections, Mermaid diagram retained as a code block, SVG image
   references included at the top of Section 3).
2. Create a subfolder at `~/Documents/Claude_code/define-e2e-flow/{slug}/` — each flow gets its own folder.
3. Save all files into that subfolder:
   - `~/Documents/Claude_code/define-e2e-flow/{slug}/{slug}-e2e-flow.md`
   - `~/Documents/Claude_code/define-e2e-flow/{slug}/{slug}-overview.svg`
   - One descriptively named SVG per diagram (e.g., `{slug}-provisioning.svg`, `{slug}-login.svg`,
     `{slug}-usecase.svg`) — use the flow name, not a generic number
4. SVG image references in the markdown use relative paths (just filenames, same folder):
   ```markdown
   ![Overview](./{slug}-overview.svg)
   ![Provisioning Flow](./{slug}-provisioning.svg)
   ```
5. Confirm at the end of your response listing every file saved with its full path.

---

## Tone and style notes

- Be direct and informative, not hedging. "The auth service validates the token" not "the auth service possibly
  validates the token based on the information provided."
- When you don't know something, say it crisply in the gaps/questions sections rather than sprinkling
  uncertainty throughout the narrative.
- The goal is to give the reader a clear mental model AND a clear picture of where that model has holes.
  A well-flagged gap is more valuable than a gap papered over with vague language.
- Use the exact section headers and structure above — it makes the output scannable and reusable.
