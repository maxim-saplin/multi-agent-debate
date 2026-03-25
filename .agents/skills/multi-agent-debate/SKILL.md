---
name: multi-agent-debate
description: Runtime playbook for running two high-capability subagents in structured debate with a consolidator. Use when a task has legitimate competing interpretations, meaningful trade-offs, or uncertainty that should stay visible instead of being flattened.
---
# Multi-Agent Debate

Use this skill to force independent reasoning, explicit critique, and honest consolidation. It is a runtime playbook, not a design notebook. Read `references/protocol-artifacts.md` only when you need the live schemas.

## Use When
- The task has real competing interpretations or trade-offs.
- The cost of a wrong answer is high enough to justify extra latency.
- A single-agent draft would likely hide assumptions, contradictions, or unresolved uncertainty.

## Do Not Use When
- The task is trivial, mechanical, or mostly lookup.
- There is only one obvious valid answer and debate would be theater.
- The task packet is too thin to support meaningful disagreement. Repair inputs first.

## Roles
- **Orchestrator**: decides whether debate is worth it, builds the packet, assigns lenses, runs validation, launches branches, and hands the record to the consolidator.
- **Branch A / Branch B**: independent high-capability subagents. Default pairing in this repo: `gpt-max` and `opus-max`.
- **Consolidator**: decision layer, not a third debater. Default in this repo: `opus-max`.

## Core Contract
Every receiving agent starts with **no conversation history**. Put all required context in the prompt. Do not assume prior turns, files, or reasoning are visible.
A standard run records:
1. `TaskPacket`
2. `LensAssignment` for each branch
3. `PositionMemo` from each branch
4. `PreCritiqueValidation` for each memo
5. `CrossCritique` from each branch, or one targeted challenge note when divergence is low
6. `ResolutionMatrix`
7. `DecisionPackage`
Hard rules:
- Branches do not read each other before finishing their own memo.
- The consolidation handoff includes both `LensAssignment` artifacts.
- Each claim separates packet support from inference.
- Recommendations separate recommendation confidence from predicted outcome confidence.
- Unresolved high-impact disagreement stays visible instead of being averaged away.
- If a step fails, label degraded mode explicitly.

## Workflow
### 1. Fit Check
Decide whether debate is justified. If not, answer directly or use a lighter review path.

### 2. Build `TaskPacket`
The packet is the shared source of truth. Include objective, decision question, scope, non-goals, allowed evidence or source packet, success criteria, explicit unknowns, and constraints on tools, time, and format. If the packet cannot support meaningful disagreement, stop and repair it before launching branches.

### 3. Assign Complementary Lenses
Give the two branches different but legitimate lenses on the same task. Good pairs create productive tension, such as evidence sufficiency vs execution practicality, short-term optimization vs long-term maintainability, or local wins vs system-level effects. Avoid mirrored assignments that will produce duplicate summaries.

### 4. Round 1: Independent `PositionMemo`
Each branch gets the same `TaskPacket` plus its own `LensAssignment`. Each memo must separate claims, assumptions, uncertainty, likely failure modes, strongest counterarguments, and what would change the branch's mind.

### 5. Validation Gate
Run `PreCritiqueValidation` on both memos before critique.
- Allow one repair pass only if structure, packet-only compliance, or recommendation fields are missing.
- Treat scope creep or outside-packet action framing as validation failure even when it sounds practical.
- If divergence is low, skip full debate overhead and request one targeted challenge note instead.
- If a branch remains unusable, continue only under an explicit degraded-mode label.

### 6. Round 2: `CrossCritique`
Each branch critiques the other memo against the same packet.
Each critique must:
- start with the target's strongest points
- classify material disagreements with the required taxonomy
- challenge unsupported claims, hidden assumptions, outside-packet reasoning, and overconfidence
- allow thesis-level and structure-level critique, not only claim-level disputes
- say whether the disagreement is resolvable from the packet

If a critique mostly restates the original memo, treat it as weak input.

### 7. Forced Consolidation
Stop after critique. Do not allow open-ended back-and-forth.
The consolidator must:
- review the whole record as neutral `Branch A` and `Branch B` where feasible
- prefer supported claims over eloquent claims
- preserve unresolved high-impact disagreement
- write `ResolutionMatrix` before `DecisionPackage`
- cite supporting branch fragments when synthesizing a third option

## Smallest Runnable Use
For a minimal honest run:
1. Build one compact `TaskPacket`.
2. Assign two different lenses.
3. Collect two independent `PositionMemo` artifacts.
4. Run the validation gate.
5. Collect either two `CrossCritique` artifacts or one targeted challenge note when divergence is low.
6. Ask the consolidator for `ResolutionMatrix` and `DecisionPackage`.
If one branch fails before critique, do not fake a full debate. Switch to the matching degraded mode and report the missing challenge explicitly.

## Required Disagreement Taxonomy
Use one primary tag for each material disagreement:
- `factual`: disagreement about what the packet actually says
- `interpretive`: same facts, different meaning
- `scope`: disagreement about what the task should optimize for
- `risk`: disagreement about downside, cost, reversibility, operational exposure, or user impact
- `missing-evidence`: the packet cannot confidently resolve the issue

If more than one applies, choose the primary blocker and note secondary nuance in free text.

## Degraded Mode
Use explicit labels. Never imply a full debate happened when it did not.
- `single-branch-complete`: one branch succeeds and the other fails or times out. Add `MissingCounterarguments` and lower confidence.
- `one-branch-shallow`: one branch is too thin for serious critique. Salvage supported points only and treat that branch as low-confidence input.
- `missing-critique`: memos exist, but one or both critiques are missing or invalid. State which challenge inputs are missing and add `UnchallengedIssueList`.
- `both-branches-weak`: both branches are weak. Stop and return missing prerequisites instead of manufacturing consensus.
- `underspecified-task`: the packet cannot resolve a high-impact blocker. Return an uncertainty-preserving answer plus the next data needed.

## Stop Conditions
- Maximum normal flow: `PositionMemo` then `CrossCritique`.
- Only exception: one targeted rebuttal note on a single blocking point.
- If validation fails twice, packet-only violations persist, or remaining disagreement is low-value, stop and consolidate honestly.

## Final Reporting
Report:
- the final decision or synthesized answer
- major resolved disagreements
- major unresolved disagreements
- confidence and why
- missing evidence or next steps

Do not dump raw branch outputs unless the user asks.
