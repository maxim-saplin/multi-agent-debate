# Three-Agent Mode

Use this mode only when the user explicitly asks for 3 agents, three agents, or a three-way debate. Do not infer this mode from task difficulty alone.

## Roles
- Orchestrator: fit check, packet, lenses, validation, launch, and handoff.
- Branch A: `gpt-max`
- Branch B: `opus-max`
- Branch C: `gemini-max`
- Consolidator: separate neutral invocation. It is not Branch C.

If `gemini-max` is unavailable, surface the blocker instead of silently downgrading to 2-agent mode.

## Workflow
### 1. Activation and `TaskPacket`
Confirm that the user explicitly asked for 3 agents, then build one shared `TaskPacket`. If the packet cannot support meaningful disagreement across three distinct lenses, repair it before launch or recommend returning to 2-agent mode explicitly.

### 2. Assign Three Complementary Lenses
Each branch needs a legitimate, non-mirrored lens on the same packet. Good triads separate evidence sufficiency, implementation practicality, and system-level or long-term effects. Each `LensAssignment` should include a distinct `anti_goal`.

### 3. Round 1: Independent `PositionMemo`
Launch all three branches with the shared `TaskPacket` plus their own `LensAssignment`. Branches stay blind to each other until all planned memos are complete.

### 4. Validation Gate
Run `PreCritiqueValidation` on all three memos before critique.
- Allow one repair pass per branch only.
- If divergence is low across the set, replace the full critique round with one `TargetedChallengeNote` per branch on the single highest-value opposing point.
- If one branch remains unusable after repair, continue only under an explicit degraded-mode label. Do not describe the result as a full 3-agent debate.

### 5. Round 2: Single `CrossCritique` Pass
Each branch emits one `CrossCritique` artifact.

In 3-agent mode, each `CrossCritique` must include two `target_reviews[]` entries: one for each peer branch. Stop after this round. Do not open a second critique loop.

If a critique skips one peer, mostly paraphrases peer memos, or avoids the strongest disagreement, treat it as shallow input or missing critique.

### 6. Forced Consolidation
The consolidator reviews the whole record neutrally across all active branches, writes `ResolutionMatrix` with one `branch_positions[]` entry per active branch on each material issue, and preserves unresolved high-impact disagreement instead of averaging three opinions into false consensus.

If the consolidator synthesizes a third option, it must cite which branch fragments support it.

## Minimal Honest Run
1. Build one compact `TaskPacket`.
2. Assign three distinct lenses.
3. Collect three independent `PositionMemo` artifacts.
4. Run the validation gate.
5. Collect either three `CrossCritique` artifacts or one `TargetedChallengeNote` per branch.
6. Ask the consolidator for `ResolutionMatrix` and `DecisionPackage`.

If one planned branch is missing, say so explicitly and switch to degraded mode. Do not describe the result as a full 3-agent debate.

## Degraded Modes
Use explicit labels. Never imply a full 3-agent debate happened when it did not.
- `limited-branches`: one or more planned branches fail or time out before a full 3-agent debate can happen. Add `MissingCounterarguments`.
- `shallow-branch-input`: one or more branches are too thin for serious critique. Salvage supported points only from shallow inputs.
- `missing-critique`: one or more required peer reviews are missing, invalid, or skip a planned peer. Add `UnchallengedIssueList`.
- `weak-branch-set`: the surviving branch set is too weak to support honest consolidation. Stop and return missing prerequisites.
- `underspecified-task`: the packet cannot resolve a high-impact blocker. Return an uncertainty-preserving answer plus the next data needed.