# Handoff

Where the thinking stands, what is settled, what is open, and what was learned by
measuring rather than reasoning.

## Settled

Each of these was argued through and stress-tested. Treat as decided unless you have a
reason to reopen one.

| # | Decision |
|---|---|
| 1 | **Two atoms.** Requirement is the unit of presentation; criterion is the unit of addressing. |
| 2 | **Ids are stable and never reused.** Sort by id. Status changes colour, never position. |
| 3 | **Two registers.** Plain English to read (≤14 words, no invented vocabulary), EARS to contract. |
| 4 | **Draft plainly, commit precisely.** Type a sentence, snap it into the frame, correct the blanks. The correction *is* the approval. |
| 5 | **The contract is code-only.** Other artifacts may be linked, never contracted. |
| 6 | **The test is the anchor.** No hashes, no drift engine. Drift is a named test failing. |
| 7 | **State is derived from evidence, never stored.** |
| 8 | **Markdown files in the repo.** Decisions in `specs/*.md`; test results in a gitignored sidecar. |
| 9 | **Blocking is mechanically decidable.** Edits the spec → block. Edits only code → assume, and mark it. |
| 10 | **Questioning stops when the next question cannot change a task.** |
| 11 | **Two escapes**, never one: "something else" and "talk it through". |
| 12 | **Impact is derived from per-option task counts**, never self-reported. |
| 13 | **Bring your own agent.** The contract lives in the prompt and an executable validator. |

## Three subsystems deleted rather than built

Worth noting as a pattern — each time, the lazy option was also the correct one.

- **Hash-based drift detection** → the test runner already is one.
- **A cloud execution platform** → the user's own machine already is one.
- **An agent harness** → whatever agent they already have already is one.

The recurring shape: something in the user's existing environment already does the job,
and building a parallel version of it adds maintenance without adding capability.

## Measured, not assumed

| Claim | Evidence |
|---|---|
| Hand-maintained status rots | Deriving state disagreed with **8 of 34** hand-written statuses — 24% — on a fixture written carefully an hour earlier. One rule showed "built" while its test was failing. |
| Self-reported impact is unstable | Run-to-run question-count spread went from **4 to 0** when impact was derived instead of reported. A weaker model went from **0/4 to 4/4** on spec-vs-code classification. |
| Audit rules belong in the prompt | Moving them from post-hoc check into the prompt cut a grounded planning run **185s → 94s** by eliminating the repair round-trip. |
| Grounding changes question quality | Identical prompt: API-only asked *"how do reviewers sign in?"*; repo-grounded asked *"does an unresolved comment stop a rule reaching agreed?"* |
| Schemas are not enforcement | Providers returned objects missing fields declared required under `strict: true`. |

## Open

None of these block progress; all of them will need an answer eventually.

- **Task generation and execution.** Rules, state and the contract work. Nothing yet
  writes a task list or runs it, so `tasked` and `built` are only reachable by hand. This
  is the largest gap and mostly a repeat of machinery that already exists: same agent
  adapter, same audit-and-repair shape, writing tasks instead of rules.
- **Dampening is weak.** Answering a question reduces related questions' impact, but
  rarely to zero, so auto-demotion to assumptions seldom fires. The constant needs tuning
  against real specs, not invented ones.
- **Multi-user.** Ownership, who may agree a rule, what happens when two people edit the
  same rule on different branches.
- **Non-code artifacts.** Currently linked but never contracted. Whether a weaker
  "reviewed by a human on date X" state is worth having is unresolved.
- **Does altitude navigation earn its cost?** Untested above ~40 rules. Below that, an
  outline is probably enough.

## If you are picking this up

Build in this order. Each step is only worth doing if the previous one held.

1. **The file format and its round-trip test.** Everything else assumes it. If markdown
   cannot carry plain + exact + anchors + rationale losslessly, decision 8 is wrong and
   you want to know immediately.
2. **The validator.** Encode the rules from [the contract](docs/02-the-contract.md) as a
   script that fails the build. A spec that enforces its own rules on itself is the
   smallest honest version of this.
3. **State derivation from a real test run.** Name a few tests after rule ids and watch
   the honest picture appear. Expect it to be worse than the hand-maintained one.
4. **The question generator.** Highest risk, and the part that decides whether any of this
   is a product. Validate that impact estimates are stable across runs *before* building
   any UI on top of them.
5. **Task generation.** Only once questions are good.

Deliberately last: the interface. Every idea here is testable from a terminal, and the
temptation to build a client first is how you end up with a beautiful shell over an
unvalidated core.
