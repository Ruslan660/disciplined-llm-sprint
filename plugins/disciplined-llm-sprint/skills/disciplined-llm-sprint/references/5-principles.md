# The Five Principles of Disciplined LLM-Assisted Development

These are the five principles the disciplined-llm-sprint workflow is built on. They are summarized in `SKILL.md`; this file is the deeper rationale for each.

The principles come from one practitioner's writeup ("Пять принципов работы с LLM", Habr, 2026) and have been validated in production sprint work.

## 1. TDD до первой строчки

**The principle:** Write a failing test before any business-logic or service code. The LLM tends to "implement first, validate later" — which lets regressions slip in. A failing test forces the LLM and the human to align on the exact behavior being added.

**Where it applies:**
- Business logic, services, repositories, parsers, validators.

**Where it does not apply:**
- UI views — these are captured via snapshot tests after implementation, because the design surface is the test (you want to see what was built, not prescribe pixel-by-pixel).

**Anti-pattern:** "Let me first sketch the implementation and then write tests." That ordering produces tests shaped by the implementation rather than tests shaped by the requirement.

## 2. Documentation as external memory

**The principle:** LLMs forget. Treat documentation as the persistent memory of the project across sessions.

**Five levels of documentation:**

| Level | Where | Purpose |
|---|---|---|
| Design spec | `docs/superpowers/specs/` | What and why we're building |
| ADR | `docs/adr/` | Why this technical decision, not the alternatives |
| Sprint plan | `docs/superpowers/plans/` | Exact step-by-step execution |
| Project CLAUDE.md | repo root or subdirectory | Rules for the LLM in this project |
| Knowledge base | second-brain / wiki / `docs/` | Durable concept-level knowledge across sprints |

At the start of every sprint, the LLM reads: current milestone spec, current sprint plan, CLAUDE.md, latest knowledge-base log entry.

**Anti-pattern:** "I'll keep it in my head until the end of the sprint." The LLM does not have that head — and the human's head will lose precision after one weekend.

## 3. Regression tests on reference data

**The principle:** Pin known-good behavior with reference data so future changes show as diffs against a baseline.

**Forms:**
- Snapshot UI tests (LTR/RTL, light/dark, multiple devices).
- Golden JSON tests (parser output, API response shape).
- Golden stream tests (SSE / WebSocket / event stream fixtures).
- Migration fixtures (database schema before/after each migration).

**When to record baselines:** after implementation passes manual verification, not before.

**Anti-pattern:** Skipping snapshot/golden tests because "the code is too simple to regress". The simpler the code, the cheaper the snapshot — record it.

## 4. Code review with the "не понял — не мерджу" rule

**The principle:** Walk every commit on the sprint branch with the human. Anything they cannot explain back in their own words is a defect: either insufficient documentation, an unclear implementation, or both. Fix it before merge.

**Why:** LLM-generated code can look production-ready while hiding subtle assumptions the human never internalized. The walkthrough surfaces those assumptions.

**Outputs of the walkthrough:**
- Concept-page candidates (for Phase 6 knowledge-base update).
- Refactor candidates ("this would be clearer if we…").
- ADR candidates ("this decision deserves a record").

**Anti-pattern:** "I trust the tests, I don't need to read the code." Tests check behavior; code review checks comprehension.

## 5. Hard scope boundaries

**The principle:** LLMs are eager. Without explicit guard-rails, they will refactor adjacent code, "improve" unrelated patterns, and stretch the sprint scope. The defense is explicit, written, repeatedly-referenced scope boundaries.

**Three guard-rails:**

1. **Project CLAUDE.md** — repo-level rules that override default LLM behavior.
2. **Sprint plan in-scope / out-of-scope sections** — read at start of every Phase 3 Block.
3. **Self-reminder loops** — at the end of each Block, ask "did anything we just did exceed the agreed scope?"

**Anti-pattern:** "Since we're already in this file, let me also fix that other thing." That is how a 3-day sprint becomes a 7-day sprint.

---

> "An LLM doesn't make a junior into a senior. It just lets the one who already knows go faster."
