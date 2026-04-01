# Two-Agent Mode

Use this mode by default when the user asks for structured debate or competing-analysis support without explicitly asking for three agents.

## Roles
- Orchestrator: fit check, packet, lenses, validation, launch, and handoff.
- Branch A: `gpt-max`
- Branch B: `opus-max`
- Consolidator: separate neutral invocation, default `opus-max` in this repo.

If the same agent family is used for Branch B and the consolidator, the consolidator pass must be a fresh neutral invocation that does not reuse Branch B framing.

## Workflow
### 1. Fit Check and `TaskPacket`
Decide whether debate is justified. Build one shared `TaskPacket` with objective, decision question, scope, non-goals, allowed sources, constraints, success criteria, and explicit unknowns. If the packet is too thin to support meaningful disagreement, stop and repair it before launch.

### 2. Assign Two Complementary Lenses
Give the branches different but legitimate lenses on the same packet. Good pairings create productive tension, such as evidence sufficiency vs execution practicality or local wins vs system-level effects.

### 3. Round 1: Independent `PositionMemo`
Launch both branches with the same `TaskPacket` plus their own `LensAssignment`. Each branch stays blind to the other until both memos are complete.

### 4. Validation Gate
Run `PreCritiqueValidation` on both memos before critique.
- Allow one repair pass only.
- Treat packet-only violations, missing structure, or missing recommendation fields as blocking issues.
- If divergence is low, replace the full critique pair with one `TargetedChallengeNote` on the highest-value opposing point.
- If a branch remains unusable after repair, continue only under an explicit degraded-mode label.

### 5. Round 2: `CrossCritique` or `TargetedChallengeNote`
In the normal path, each branch emits one `CrossCritique` artifact with exactly one `target_reviews[]` entry for the peer branch.

In the low-divergence path, collect one `TargetedChallengeNote` instead of the full pair of critiques.

If a critique mostly paraphrases the source memo or avoids the strongest disagreement, treat it as shallow input.

### 6. Forced Consolidation
Stop after critique. Do not allow open-ended back-and-forth.

The consolidator must review the whole record neutrally, prefer supported claims over eloquent claims, write `ResolutionMatrix` before `DecisionPackage`, and cite supporting branch fragments when synthesizing a third option.

## Minimal Honest Run
1. Build one compact `TaskPacket`.
2. Assign two different lenses.
3. Collect two independent `PositionMemo` artifacts.
4. Run the validation gate.
5. Collect either two `CrossCritique` artifacts or one `TargetedChallengeNote`.
6. Ask the consolidator for `ResolutionMatrix` and `DecisionPackage`.

If one branch fails before critique, do not pretend a full debate happened. Switch to degraded mode and name the missing challenge explicitly.

## Degraded Modes
Use explicit labels. Never imply a full debate happened when it did not.
- `limited-branches`: one planned branch fails or times out before a full debate can happen. Add `MissingCounterarguments`.
- `shallow-branch-input`: one branch is too thin for serious critique. Salvage supported points only from that branch.
- `missing-critique`: memos exist, but one or more required critiques are missing or invalid. Add `UnchallengedIssueList`.
- `weak-branch-set`: the surviving branch set is too weak to support honest consolidation. Stop and return missing prerequisites.
- `underspecified-task`: the packet cannot resolve a high-impact blocker. Return an uncertainty-preserving answer plus the next data needed.