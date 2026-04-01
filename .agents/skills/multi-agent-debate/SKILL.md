---
name: multi-agent-debate
description: "Runtime playbook for structured 2-agent debate by default, or explicit 3-agent debate when the user asks for three agents. Use when a task has legitimate competing interpretations, material trade-offs, or uncertainty that should stay visible instead of being flattened."
---
# Multi-Agent Debate

Use this skill to force independent reasoning, explicit critique, and honest consolidation. This file is the entrypoint. Read `references/protocol-artifacts.md` only when you need the live schemas, then use the mode doc that matches the requested agent count.

## Use When
- The task has real competing interpretations or trade-offs.
- The cost of a wrong answer is high enough to justify extra latency.
- A single-agent draft would likely hide assumptions, contradictions, or unresolved uncertainty.

## Do Not Use When
- The task is trivial, mechanical, or mostly lookup.
- There is only one obvious valid answer and debate would be theater.
- The task packet is too thin to support meaningful disagreement. Repair inputs first.

## Choose the Mode
- Default to `references/two-agent-mode.md`.
- Switch to `references/three-agent-mode.md` only when the user explicitly asks for 3 agents, three agents, or a 3-way or three-way debate.
- In 3-agent mode, `gemini-max` is Branch C. The consolidator stays separate and neutral.
- If the user asks for debate but does not name the agent count, stay in 2-agent mode.

## Shared Contract
Every receiving agent starts with no conversation history. Put all required context in the `TaskPacket`.

Use the shared schemas in `references/protocol-artifacts.md`. Across both modes:
- The orchestrator does the fit check, builds the packet, assigns complementary lenses, runs validation, launches branches, and hands the full record to the consolidator.
- Branches do not read each other before finishing their own `PositionMemo`.
- Every claim separates packet support from inference.
- Recommendations separate recommendation confidence from predicted outcome confidence.
- Unresolved high-impact disagreement stays visible instead of being averaged away.
- If a planned step or branch fails, label degraded mode explicitly.
- Stop after one critique round. The only exception is one targeted rebuttal note on a single blocking point.
- Do not dump raw branch outputs unless the user asks.

## Required Disagreement Taxonomy
Use one primary tag for each material disagreement:
- `factual`: disagreement about what the packet actually says
- `interpretive`: same facts, different meaning
- `scope`: disagreement about what the task should optimize for
- `risk`: disagreement about downside, cost, reversibility, operational exposure, or user impact
- `missing-evidence`: the packet cannot confidently resolve the issue

If more than one applies, choose the primary blocker and note secondary nuance in free text.

## Final Reporting
Report:
- the final decision or synthesized answer
- major resolved disagreements
- major unresolved disagreements
- confidence and why
- missing evidence or next steps

Do not dump raw branch outputs unless the user asks.

## References
- Shared artifacts: `references/protocol-artifacts.md`
- Default workflow: `references/two-agent-mode.md`
- Explicit 3-agent workflow: `references/three-agent-mode.md`
