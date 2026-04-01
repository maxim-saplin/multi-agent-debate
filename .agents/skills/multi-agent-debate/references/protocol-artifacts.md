# Protocol Artifacts

Use these schemas for live runs only. Keep them compact and specific to the task at hand. These shared artifacts work for both the default 2-agent mode and the explicit 3-agent mode.

## `TaskPacket`

```text
TaskPacket
- objective:
- decision_question:
- scope:
- non_goals:
- allowed_sources:
- source_packet:
- constraints:
- success_criteria:
- explicit_unknowns:
- output_format:
```

Notes:

- `decision_question` should force a choice, synthesis, or bounded recommendation.
- `allowed_sources` should make packet-only boundaries explicit.
- `explicit_unknowns` should name the highest-impact missing information up front.

## `LensAssignment`

```text
LensAssignment
- branch_label:
- primary_lens:
- what_to_prioritize:
- likely_blind_spots:
- anti_goal:
```

Notes:

- `branch_label` should stay stable across the run, such as `Branch A`, `Branch B`, or `Branch C`.
- Lenses should create productive tension, not theater.
- `anti_goal` helps prevent mirrored memos.

## `PositionMemo`

```text
PositionMemo
- branch_label:
- thesis:
- key_claims[]:
  - claim_id:
  - claim:
  - packet_support:
  - inference:
  - confidence:
  - confidence_kind: claim|recommendation|prediction
  - outcome_confidence: optional, required when confidence_kind is recommendation
- assumptions[]:
- uncertainty[]:
- likely_failure_modes[]:
- strongest_counterarguments[]:
- what_would_change_my_mind[]:
```

Rules:

- `packet_support` contains only allowed-packet material.
- `inference` captures reasoning drawn from the packet. If none is needed, write `none`.
- `confidence` is local to the claim, not a blanket run score.
- `strongest_counterarguments` must be written before cross-read.

## `PreCritiqueValidation`

Run this after both memos and before critique. Surface it explicitly whenever repair, targeted challenge, or degraded mode is involved.

```text
PreCritiqueValidation
- branch_label:
- structurally_complete: yes|no
- packet_only_compliant: yes|no
- recommendation_fields_complete: yes|no|n/a
- divergence_signal: high|medium|low
- repair_needed: yes|no
- blocking_issues[]:
```

Rules:

- Allow one repair pass only.
- Treat out-of-scope or outside-packet action framing as a blocking issue even when it sounds useful.
- If `divergence_signal: low`, prefer one targeted challenge note over a full critique loop.
- In a multi-branch run, judge `divergence_signal` against the current branch set, not only one peer.

## `TargetedChallengeNote`

```text
TargetedChallengeNote
- branch_label:
- target_branch:
- focus_issue:
- why_it_matters:
- required_update:
- resolvable_from_packet: yes|no|partly
```

Rules:

- Use this only when divergence is low and a full critique round would be theater.
- `target_branch` names the peer branch whose claim or framing needs the most valuable challenge.
- `required_update` should state the specific revision that would reduce the disagreement.

## `CrossCritique`

```text
CrossCritique
- branch_label:
- target_reviews[]:
  - target_branch:
  - strongest_points_in_target[]:
  - disagreements[]:
    - disagreement_id:
    - type: factual|interpretive|scope|risk|missing-evidence
    - target_level: claim|thesis|structure
    - target_claim_id:
    - issue:
    - why_it_matters:
    - resolvable_from_packet: yes|no|partly
    - required_update:
  - claims_i_concede[]:
  - remaining_non_negotiables[]:
```

Rules:

- One `CrossCritique` artifact may cover one target in 2-agent mode or multiple targets in 3-agent mode.
- Start each target review with the target's strongest points.
- `required_update` must be actionable.
- Use `target_level: thesis` or `target_level: structure` when the problem is broader than one claim.
- Call out outside-packet reasoning explicitly.

## `ResolutionMatrix`

```text
ResolutionMatrix
- issue_id:
- branch_positions[]:
  - branch_label:
  - position:
- status: accepted|accepted_with_caveat|rejected|unresolved
- rationale:
- winning_evidence_or_reason:
- follow_up_needed:
- convergence_audit: independent-divergent|independent-convergent|possibly_derivative|not_applicable
- synthesized_option: optional
```

Rules:

- Every material disagreement from critique should appear here.
- Use one row per material claim or synthesized issue that matters to the final decision.
- `branch_positions` should include each active branch exactly once for that row.
- `unresolved` is valid when the packet cannot settle the issue with confidence.
- If the consolidator creates a new option, cite which branch fragments support it.

## `DecisionPackage`

```text
DecisionPackage
- final_answer:
- why_this_is_the_best_current_resolution:
- resolved_disagreements[]:
- unresolved_disagreements[]:
- confidence:
- major_assumptions[]:
- next_data_needed[]:
- recommended_next_steps[]:
- run_mode: full-debate|limited-branches|shallow-branch-input|missing-critique|weak-branch-set|underspecified-task
- planned_branch_count:
- active_branches[]:
- MissingCounterarguments[]: optional, required in `limited-branches`
- UnchallengedIssueList[]: optional, required in `missing-critique`
```
