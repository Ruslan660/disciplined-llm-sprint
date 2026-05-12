# Block Recap + Hands-on Rule — Full Rationale

This file expands the rule summarized in `SKILL.md` Phase 3.

## The rule

After every Block in Phase 3, the skill produces:

1. A **detailed recap** of what was built (files, commits, verifications, gotchas).
2. A **hands-on exercise** for the user.
3. An **explicit gate**: ask "ready for the next Block?" and wait for confirmation.

## Why both parts are required

| Combination | Result |
|---|---|
| Recap only | Passive consumption. The user reads, nods, moves on. Three Blocks later, they cannot explain what was built. |
| Hands-on only | No anchor. The user does an exercise but cannot connect it back to the work that just happened. |
| Both | The recap creates the reference; the hands-on creates the embodied understanding. Together they convert work-done into knowledge-acquired. |

## The "30-40% hands-on" target

The disciplined-llm-sprint workflow targets 30-40% of the user's time spent in hands-on activity (writing implementations, reviewing diffs in detail, completing exercises). Below this, the user is being read-to, not collaborated with. Above this, the LLM is being underused as a force multiplier.

The Block Recap rule is the operational mechanism that produces this ratio. Without it, the user's hands-on time drops to ~10% (review of finished code only).

## Hands-on options

Pick the one that best fits the Block's content:

- **Manual UI check** — open the running app, exercise the feature, look at it.
- **Read-and-explain-diff** — the user reads the commit diff and explains it back in their own words. The skill listens for misunderstandings.
- **Modify-and-observe** — the user makes a small modification and observes the effect. "Change this parameter and rerun the test; what changes?"
- **Reflective question** — "Why did we implement it this way and not [alternative]?" The user thinks through the trade-off.

The exercises are deliberately small (2-10 minutes each) — the discipline is in the frequency, not the depth of any single exercise.

## When to skip

A purely mechanical Block — file rename, dependency version bump, generated-code commit, lint cleanup — does not require hands-on. In these cases, the skill **explicitly says** "Hands-on skipped because this Block is mechanical." The exception must be visible, not silent.

If the skill catches itself skipping hands-on without an explicit reason, that is a signal that either:
- The Block is too large (split it).
- Or the user is being trained out of engagement (slow down, reintroduce hands-on).

## Common failure modes

| Failure mode | Symptom | Correction |
|---|---|---|
| Skill stops doing recaps after the third Block | User says "ок" faster each Block, skill drops the recap | Reintroduce the recap explicitly on the next Block; flag in retro. |
| Recaps become very short | "Did X. Block done." | Lengthen — at minimum: files, commits, verifications. |
| Hands-on always defaults to "read the diff" | Same exercise every Block | Vary by Block content; reflective questions are underused. |
| User completes hands-on but skill does not check it | Skill moves on without feedback | Always close the loop — review the hands-on output before saying "next Block". |

## Retroactive enforcement

If during Phase 7 retro the user reports "the sprint felt fast and confusing", the most likely failure is dropped recaps in Phase 3. Action: re-walk the affected Blocks in Phase 5 (code review) and surface concept pages for everything that became fuzzy.
