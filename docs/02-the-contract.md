# The contract

The contract is the binding between a rule and the thing that satisfies it. Everything
else in spec-driven development is bookkeeping; this is the part that makes a spec worth
keeping.

## The test is the anchor

The obvious approach is to record which code satisfies which rule, hash it, and alarm when
the hash changes. It is also wrong, and wrong in a way that destroys the product: **hashes
fire on every harmless refactor.** Rename a variable, get a drift alert. Within a week
people ignore the one signal the system exists to produce.

A test named for the rule does the whole job:

```js
test("BOND-2.2 flags code that no longer matches the rule", () => { … })
```

- Code changes and breaks the rule → the test fails → *that* is drift.
- Code changes harmlessly → the test passes → correctly silent.
- The rule changes and the test does not → the rule drops out of verified.

No hash store, no drift engine, no separate anchor file to go stale. The query is
`grep BOND-2` in your test output. The test runner you already have is the drift detector.

This deletes a subsystem rather than building one, which is generally the sign of getting
a design right.

## State is derived, never stored

Status fields lie. Someone sets "done" and moves on; nothing revisits it.

So do not store status. Compute it from evidence that cannot be faked:

```
test fails        → drifted
test passes       → verified
code anchored     → built
task exists       → tasked
human agreed      → agreed
otherwise         → drafted
```

Nobody ever sets a status by hand, so nobody can be wrong about one.

> **Measured:** on a 34-rule fixture written carefully by hand an hour earlier, deriving
> state disagreed with the hand-written status on **8 of 34 rules — 24%**. Seven were
> rules whose tests already passed but whose status was never updated. One was showing as
> built while its test was failing. That last case is the entire product in one line.

An important consequence: `built` and `verified` are *independent* evidence, not a
sequence. A passing test that names a rule is sufficient on its own; requiring a separate
code anchor as well is a leftover from the hash design and should be removed with it.

## Code only

The temptation is to let anything satisfy a rule — a design, a document, a decision.
Resist it.

For code, "does this still satisfy the rule?" is answerable by running something. For a
design document it is a *judgement*, which means an LLM verdict, which means the contract
is probabilistic. Then `verified` stops meaning anything and the green state is theatre —
which kills the one property that makes this better than a wiki.

Two grades of link, and only one is a contract:

- **satisfies** — mechanically verifiable, gates merge. Code only.
- **relates to** — a link for humans. No enforcement, no state.

Non-code artifacts are worth linking. They are not worth pretending to check.

## Enforcement

The gate is a script that runs in CI and refuses to be clever:

- Every rule id referenced by a test exists in the spec.
- No rule claims code that no task asked for.
- No rule claims a task before a human agreed to it.
- Plain readings are within length and free of invented vocabulary.
- Requirements appear in id order.

Failing the build on these is what stops the spec becoming decoration. A spec that
enforces its own rules on itself is the smallest honest version of this idea.
