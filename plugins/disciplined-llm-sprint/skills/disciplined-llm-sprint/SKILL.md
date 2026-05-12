---
name: disciplined-llm-sprint
description: Use when starting a substantial new feature or sprint that needs disciplined LLM-assisted execution — TDD, atomic commits, lectures for newly-introduced concepts, hands-on exercises for the user, code-review walkthrough, retrospective, optional knowledge-base integration. Trigger explicitly via /disciplined-llm-sprint or when the user describes work as "new sprint / new feature / new milestone / new module". Skips for one-off bug fixes — use /gsd-quick or /gsd-debug instead.
---

# Disciplined LLM Sprint

## Overview

Codify a repeatable, validated workflow for LLM-assisted software development that prevents the failure modes typical of casual LLM usage: hidden regressions, scope creep, undocumented knowledge loss, drift between assistant and user understanding.

After invocation, this skill walks the user through a fixed 7-phase script. Each phase ends with an explicit **gate** — a blocking checkpoint where the user must confirm before the skill moves on. The skill orchestrates existing `superpowers` skills (brainstorming, writing-plans, executing-plans) and adds discipline-specific layers (scope gate, lectures, hands-on, Block Recap, retro).

**Announce at start:** "I'm using disciplined-llm-sprint to walk through a structured sprint. We'll go through 7 phases with explicit gates."

## The Five Principles

The workflow is grounded in five principles:

1. **TDD до первой строчки** — failing test first for all business logic and services. UI captured by snapshot tests post-implementation.
2. **Documentation as external memory** — 5 levels: design specs, ADRs, sprint plans, project CLAUDE.md, knowledge-base session notes.
3. **Regression tests on reference data** — snapshot UI, golden JSON, golden streams, migration fixtures.
4. **Code review with the "if I don't understand it, I don't merge it" rule** (sometimes named "не понял — не мерджу") — atomic commits + walkthrough; anything unclear becomes a knowledge-base concept page.
5. **Hard scope boundaries** — project CLAUDE.md, sprint plan in/out scope, explicit self-reminders.

See `references/5-principles.md` for the full digest.

## The Seven Phases

| # | Phase | Duration (typical) | Skill invoked |
|---|---|---|---|
| 0 | Sprint Kickoff (gate) | 5-15 min | none |
| 1 | Planning | 30-90 min | `superpowers:brainstorming` → `superpowers:writing-plans` |
| 2 | New-concept lectures (loop) | 10-30 min per concept | optional `Explore` / `gsd-phase-researcher` subagent |
| 3 | TDD Execution (per Block) | hours-days | `superpowers:executing-plans` |
| 4 | Manual verification | minutes-hour | direct tools |
| 5 | Code review walkthrough | 30-60 min | direct `git log` + `Read` |
| 6 | Knowledge-base update (optional) | 30-60 min | optional `gsd-extract_learnings` |
| 7 | Retrospective + merge | 15-30 min | `superpowers:finishing-a-development-branch` |

---

## Phase 0 — Sprint Kickoff (gate)

**Goal:** Lock in scope agreement before any planning or code.

**Steps:**

1. Ask: "What are we building? What's the scope? What's explicitly out of scope?"
2. Wait for the user's conversational answer.
3. Summarize the scope agreement in 2-4 bullets.
4. Ask: "Does this summary match what you want to build? Reply with an explicit confirmation — restate the scope in your own words, or reply with confirmation language like 'confirmed' / 'yes, exactly that'."

**Gate:** The user must confirm the scope summary explicitly. Implied agreement is not acceptable. A bare "ok" or "lgtm" is not sufficient — ask again. If the user hedges ("maybe also include X") — clarify until the scope is binary.

Once the scope is locked, transition to Phase 1.

---

## Phase 1 — Planning

**Goal:** Produce a design spec and an executable implementation plan.

**Steps:**

1. Invoke `superpowers:brainstorming` skill. Let it run its dialogue with the user — your job here is to monitor that the discussion stays within the Phase 0 scope.
2. After brainstorming finishes and the spec is committed, invoke `superpowers:writing-plans` skill. Same monitoring role.
3. When the plan is committed, identify the **new concepts** the plan introduces. Flag them in a numbered list. This list drives Phase 2.

**Gate:** The user explicitly tells Claude that the spec and plan are approved (a written commit alone is not enough — the user must also state approval in the conversation), AND the artifacts are committed. The new-concepts list is then presented; the user explicitly agrees on which deserve lectures before Phase 2 begins.

---

## Phase 2 — New-concept lectures (loop)

**Goal:** Make sure the user understands every new concept before it appears in production code.

**For each new concept identified in Phase 1:**

1. Outline a 10-30 minute lecture covering:
   - What the concept is.
   - Why it exists / what problem it solves.
   - How to use it — minimum syntax and a worked example.
   - Common pitfalls and edge cases.
   - Parallels to languages or frameworks the user already knows (helps anchoring).
2. **Heuristic:** if the lecture topic requires reading 5+ pages of external documentation, suggest delegating the research portion to an `Explore` or `gsd-phase-researcher` subagent. The subagent returns a digest; the user gets the lecture from the digest. This protects main-context tokens for actual implementation work.
3. Suggest a hands-on mini-exercise that exercises the concept in isolation. The exercise should be small (1-3 files, 10-20 lines of code) and self-contained.
4. Wait for the user to complete the exercise.
5. Review the exercise. Common issues: missed edge cases, unidiomatic syntax, misunderstanding of why the construct exists. Address each one conversationally.

**Gate per concept:** The user explicitly confirms understanding before the loop moves to the next concept. A clear affirmative ("got it" / "understood" / "Понял" — match the user's working language) = green light. Any hedge ("not quite" / "kind of" / "вроде ясно, но…") triggers an expanded explanation or a second exercise.

After all concepts are processed, transition to Phase 3.

See `templates/lecture-skeleton.md` for a suggested lecture structure.

---

## Phase 3 — TDD Execution (per Block)

**Goal:** Implement the plan via atomic RED → GREEN → REFACTOR commits, organized into Blocks. Each Block ends with a mandatory recap and hands-on for the user.

**Definitions:**

- **Block** — one logical task group from the plan, typically 3-6 tasks that share an integration goal (e.g., "set up the auth flow" or "wire the login screen to the auth service").
- **RED commit** — a failing test, committed on its own.
- **GREEN commit** — the minimal implementation that makes the failing test pass.
- **REFACTOR commit** — clean-up without changing behavior. Only included when warranted.

**Steps per Block:**

1. Announce the Block: "Starting Block N — [description]. This covers tasks X, Y, Z from the plan."
2. Invoke `superpowers:executing-plans` skill for this Block's tasks. Let it execute the TDD cycle on each task.
3. After the Block's last task is verified:
   - Mandatory: produce a **detailed recap** (see Block Recap below).
   - Mandatory: offer a **hands-on exercise** for the user (see Block Recap below).
4. **Gate:** the user explicitly confirms the recap and indicates readiness for the next Block — an affirmative such as "next" / "ok" / "let's go" / "дальше" / "поехали" (match the user's working language). **Silence or ambiguity does not count as confirmation — ask again.**

### Block Recap + Hands-on Rule (mandatory)

After every Block, the skill produces:

**1. Detailed recap covering:**
- Files changed (with full paths).
- Commit SHAs.
- Commands run and their verification results.
- Any non-obvious gotchas, decisions, or refactors.

**2. Hands-on exercise for the user. One of:**
- Manual UI check (open the running app, exercise the feature).
- Read-and-explain-diff (the user reads the commit and explains it back in their own words).
- Modify-and-observe (the user makes a small modification and observes the effect — e.g., changes a parameter and sees the test still passes, or changes the test and sees implementation needs to follow).
- Reflective question ("why did we implement it this way and not [alternative]?").

**3. Mark the task completed and ask: "Ready for the next Block?"**

**Why this rule exists:** The principle "30-40% of time hands-on for the user" requires anchoring exercises. Recap without hands-on lets the user passively consume; hands-on without recap leaves the user without a reference to anchor to. Both are required.

**Exception:** If a Block is purely mechanical (file rename, dependency bump, generated-code commit), the skill says explicitly: "Hands-on skipped because this Block is mechanical." Exceptions must be visible, not silent skips.

See `references/block-recap-rule.md` for the full rationale.

After the last Block completes, transition to Phase 4.

---

## Phase 4 — Manual verification

**Goal:** Confirm the sprint deliverables work end-to-end before reviewing code.

**Steps by project type:**

- **UI-touching work:** Manual smoke test in the project's runner — iOS Simulator, Android emulator, browser, or whatever the project uses. Test the golden path of the new feature plus 1-2 obvious edge cases.
- **Backend / CLI work:** Integration test or smoke script that exercises the new feature end-to-end. Curl + jq, a Postman collection, or a project-local script.
- **Visual UI:** Snapshot tests if the framework supports them. Multi-axis coverage recommended — LTR/RTL, light/dark, multiple device sizes — proportional to the sprint scope.

**Known pattern:** pixel-perfect snapshot tests on CI typically fail across environment differences (OS subminor versions, font hinting, antialiasing). Three resolution strategies:

1. **CI-recorded baselines** — record once on the CI runner, commit those PNGs.
2. **Perceptual diff with tuned precision** — e.g., `precision: 0.98` in `assertSnapshot`.
3. **Skip-on-CI with documented follow-up** — run snapshot tests only locally; mark this as known follow-up work.

**Gate:** the user confirms the manual verification passed for each meaningful flow. If anything is broken, fix it before Phase 5 — do not proceed to code review with known regressions.

---

## Phase 5 — Code review walkthrough

**Goal:** Walk through every commit on the sprint branch and surface anything the user does not understand.

**Steps:**

1. Run: `git log --reverse <base>..HEAD --oneline`. This is the sprint commit list.
2. For each commit, in order:
   - Run `git show <sha>` to see the diff.
   - Explain to the user what the commit does and why it does it that way.
   - User asks any clarifying question.
   - Anything still unclear → mark the topic as a concept-page candidate for Phase 6.
3. After the walkthrough, summarize the concept-page candidates list.

**Hard rule:** if the user cannot explain a commit back in their own words ("не понял — не мерджу" / "I don't understand it, I don't merge it"), that commit needs additional documentation, a clarifying refactor, or both — before merge.

**Gate:** the user confirms the walkthrough is complete and the concept-page candidates list is captured. Anything that becomes a planned concept page must surface in Phase 6.

---

## Phase 6 — Knowledge-base update (optional)

**Goal:** Capture the sprint's durable knowledge before it fades.

**If the user maintains a project knowledge base (wiki, second-brain, `docs/`, etc.):**

*How to detect:* check for a `docs/` directory, a `wiki/` directory, a `~/Work/second-brain/` path (or similar configured location), or an explicit reference in the project's CLAUDE.md. If none found, skip to "If no knowledge base exists" below.

1. **Concept pages** — write them incrementally throughout Phase 3 if not already done. Catch up any remaining concept-page candidates from Phase 5 here. One page per concept; do not batch a single page covering many concepts.
2. **Question pages** — write 2+ question pages from Q&A captured during the sprint. Use the form "When should I…", "Why use X instead of Y", "How does X work in practice".
3. **Session note** — one document summarizing the sprint scope. Use `templates/session-note.md` as a starting structure.
4. **Lint** — check for orphan pages and broken wikilinks. Fix or document them as follow-ups.
5. **Update** — index, log, and project page get cross-references for the new session note and concept pages.

**If no knowledge base exists:**

- Skip the phase.
- If the sprint introduced 3+ new concepts, suggest creating a knowledge base as a future follow-up. Do not block on it.

See `templates/session-note.md` for the suggested session note structure.

---

## Phase 7 — Retrospective + merge

**Goal:** Capture the sprint's lessons and merge the branch with full audit trail.

**Steps:**

1. **Three retro questions, one at a time:**
   - "What surprised you most in this sprint?"
   - "What was harder than you expected?"
   - "What should we do differently next time?"
2. Record the answers — in the session note (if Phase 6 happened) or in the merge commit body.
3. Invoke `superpowers:finishing-a-development-branch` to select a merge strategy.
4. Apply the chosen merge strategy. **Default for disciplined sprints:** `git merge --no-ff` to preserve atomic commit history with a single descriptive merge commit at the top. Squash merges are discouraged because they erase the RED → GREEN → REFACTOR cycle that the discipline produced.
5. Push to the remote **only with explicit user approval in this turn**. Do not rely on a prior session's permission. Branch push is a visible remote action; the user must affirmatively say "push" (or equivalent) right before you push.
6. Update the project plan's status (mark Completed, record merge commit SHA).

**Gate:** the user confirms the merge is correct (look at the merged history) and the remote state is what they expect. Sprint is closed only after this confirmation.

---

## Composition with other skills

| Phase | Invokes / Suggests | Mode |
|-------|--------------------|------|
| 1 | `superpowers:brainstorming` | sequential |
| 1 | `superpowers:writing-plans` | sequential (after brainstorming) |
| 2 | `Explore` / `gsd-phase-researcher` subagent | optional, per-lecture when research-heavy |
| 3 | `superpowers:executing-plans` | with explicit per-Block boundaries |
| 4 | (none — direct tool calls) | — |
| 5 | (none — `git log` + `Read`) | — |
| 6 | `gsd-extract_learnings` | optional, if GSD plugin available |
| 7 | `superpowers:finishing-a-development-branch` | for the merge strategy step |

This skill does not duplicate the logic of the skills it invokes. It coordinates them and adds the discipline-specific layers (Phase 0 scope gate, Phase 2 lectures, Block recap, Phase 5 walkthrough, Phase 7 retro).

---

## Failure modes and when to stop

Stop and escalate to the user if any of the following happens:

- **Phase 0 scope keeps drifting.** Three or more rephrasings without a stable answer → the work is not ready for a disciplined sprint; suggest brainstorming or domain research first.
- **Phase 2 user cannot complete a hands-on after the lecture is expanded once.** The concept is not yet teachable in this format — break it down further, or escalate as a known prerequisite for the sprint.
- **Phase 3 Block does not complete after a reasonable time and tasks keep expanding.** The plan was wrong; return to Phase 1, rewrite the plan, and proceed from a corrected baseline.
- **Phase 5 reveals a commit the user can never understand even after explanation.** Refactor for clarity before merge.
- **Phase 7 retro reveals a fundamental process failure** (e.g., "the lectures did not help me at all"). Surface as input for the next sprint planning; do not paper over it in the session note.
