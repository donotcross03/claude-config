# claude-config

Claude Code skills and configuration for public safety software product work.

## Skills

### `discovery`
Structured, hypothesis-driven discovery cycle for sales and customer conversations. Supports four commands:
- `/discovery prep` — prepare for an upcoming call (cold or warm)
- `/discovery capture` — log notes or a transcript after a call
- `/discovery synthesize` — finalize findings and commit to a problem statement
- `/discovery brief` — generate a shareable stakeholder brief (.md + .docx)

### `e2e-flow`
Maps any system, integration, or business process into a structured end-to-end flow. Takes raw inputs — meeting notes, Slack threads, architecture docs, or a brain dump — and produces a narrative walkthrough, Mermaid diagrams, SVG diagrams, gap analysis, and open questions.

Trigger: "map out this flow", "help me understand how this works", "what systems are involved", "document this process", or paste raw notes and ask for structure.

### `feature-request-refinement`
Converts ill-formed feature requests into well-formed, actionable briefs for product, design, and engineering. Three modes:
- `/frr-partner [Team, Contact]` — request from a partner team building on the platform
- `/frr-customer [Agency, Contact]` — request from an end customer / agency
- `/frr-self` — self-originated request

Output is a living `.md` brief saved to `~/Documents/Claude_code/briefs/`.

## Commands

Slash command shortcuts in `commands/`:
- `/frr-partner`, `/frr-customer`, `/frr-self` — invoke FRR in the appropriate mode
- `/pipeline` — run the full feature pipeline
- `/prototype` — build an HTML prototype
- `/decision` — log a decision to the decision journal
- `/cad-concept-brief` — generate a concept brief

## Usage

These are [Claude Code](https://claude.ai/claude-code) skills. To use them, place the `skills/` directory contents in `~/.claude/skills/` and the `commands/` contents in `~/.claude/commands/`.
