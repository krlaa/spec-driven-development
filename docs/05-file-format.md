# File format

## Markdown, in the repository

Markdown was never the problem. **Markdown as the *interface* was the problem** — editing a
wall of `#` and `-` is miserable, especially on a small screen. As a *storage* format it is
close to ideal: it diffs readably in a pull request, every agent already parses it, hosts
render it, and it outlives whatever tool wrote it.

The tool is the interface. The file is the record.

Why in the repo rather than a database: **a spec must move with code through branches.** A
feature branch changes a rule *and* the code together, and lands as one reviewable change.
A rule can be true on one branch and not another — which is exactly what a branch is. A
database cannot represent that without reinventing git badly.

## Durable facts and transient state live apart

- `specs/*.md` — decisions. Human-authored, reviewed in pull requests, changed only when
  someone decides something.
- `.spec-state.json` — test results. Written by tooling after each run, gitignored, never
  reviewed.

Keeping them apart is what stops your history filling with `state: BOND-2.2 → drifted`
commits. Spec files change when a *decision* changes; nothing else.

## The shape

```markdown
# BOND · Contract

The binding between a rule and the code that satisfies it.

---

## BOND-2 · Drift detection

As a spec owner, I want to know when code and spec disagree, so that the spec stays
worth reading.

### BOND-2.2 — If the code no longer matches the rule, we flag it.

**WHEN** anchored source changes
**THE SYSTEM SHALL** mark the criterion drifted and record what changed

`agreed` `task:T-52` `code:core/drift.js:46`

> **Why:** Considered hashing the spec text and diffing it. Rejected: hashes fire on
> every harmless refactor, so the one signal we exist to deliver gets ignored.
```

Each element earns its place:

- **The plain reading is the heading.** A host's outline sidebar becomes a table of
  contents in English, which is the cheapest possible win.
- **The exact wording sits underneath**, so scanning and contracting are separated on the
  page as well as in the model.
- **Anchors are one backtick line** that diffs on a single row.
- **Rationale is a blockquote**, present only where a real alternative was rejected.
- **No test result anywhere.** That is derived at read time from the test run.

## Compatibility is a wedge

This shape is deliberately close to [Kiro][kiro]'s `requirements.md` — user stories plus
EARS acceptance criteria. Staying compatible means an existing spec opens on day one
rather than requiring migration.

## Round-trip or it is not a format

If the file cannot be parsed back into the model losslessly, it is a rendering, not a
format. Test it: parse the markdown, compare against the model it came from, and require
exact equality — every rule, every anchor, every rationale.

That test is also what tells you the format is under-specified, usually by failing on the
one field you forgot people would need.

[kiro]: https://kiro.dev/docs/specs/
