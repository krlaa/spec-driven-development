# Atoms and registers

## A spec is addressable units, not a document

A specification written as prose has no addressable parts. You cannot point at a sentence,
attach state to it, or check whether anything satisfies it. Structure is carried by
typography, which is exactly the part that survives least.

Two units, doing different jobs:

- **Requirement** — the unit of *presentation*. A user story plus its rules. Small enough
  to hold in your head at once, big enough to be worth a title.
- **Criterion** — the unit of *addressing*. One rule, one stable id (`AUTH-3.2`). This is
  what tasks cite, what tests name, what carries state.

The split matters because the two jobs pull in opposite directions. Presentation wants
few, meaningful chunks. Addressing wants many, precise ones.

## Ids are addresses, not labels

An id is an anchor. Titles are what people read.

- Ids never change and are never reused. A deleted rule retires its number permanently.
- Sort order is **by id**, never by status or recency. Status changes colour; it never
  changes position. Reordering a spec destroys the reader's memory of where things are.
- The id is displayed quietly beside the title, not in place of it. `Drift detection ·
  BOND-2`, not `BOND-2 · Drift detection`.

A corollary that is easy to get wrong: an epic code like `BOND` for "contract" is a label
someone invented. Codes should be words a person would say — `CONTRACT`, `AUTH`, `BILLING`
— because the reader has to guess what they cover from the code alone.

## Two registers

A rule has to do two incompatible jobs: be read quickly by a person, and be precise enough
to test. One sentence cannot do both. So write it twice.

**Plain** — what the rule means, in a sentence anyone can read.

> Nothing ships while the spec and the code disagree.

**Exact** — the same rule in a constrained syntax, unambiguous and testable. [EARS][ears]
works well: a small keyword set (`WHEN`, `WHILE`, `IF`, `WHERE`) and a fixed clause order.

> **WHILE** drift is unresolved **THE SYSTEM SHALL** block merge to the default branch

The plain reading is what appears when scanning. The exact wording appears when you are
committing to it, or when a test is derived from it. Neither is a summary of the other;
both are authored.

Rules that keep the plain register honest:

- Under ~14 words. If it needs a comma and a subclause, it is two rules.
- No invented vocabulary. A term earns its place only if it is *a thing you see in the
  product* — a state, a screen, a button. "Drift" survives because you see a drift banner.
  "Identifier" and "artifact" do not; they are just *rule* and *code* dressed up.

That second rule is worth enforcing in CI. It is the difference between a spec a
non-developer can review and one only its author can.

## Altitude

Given atoms, reading a spec becomes navigation rather than scrolling. Four levels, each
rendering a *different representation* of the same thing rather than a bigger one:

```
spec         heatmap of every rule's state
epic         progress bars per area
requirement  a story and its rules
criterion    the exact wording, its anchors, and why it says this
```

The reason this is worth the trouble: atomising a spec kills narrative. "Why did we decide
this" has nowhere to live. The fix is to attach rationale to the decision itself — see
[planning](03-planning.md), where it falls out of the questioning process for free.

[ears]: https://alistairmavin.com/ears/
