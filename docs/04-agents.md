# Agents

## Bring your own agent, not your own key

Two ways to put a model behind this:

- **Bring your own key** — you hold the prompts and call an API with the user's key.
- **Bring your own agent** — the user already has a coding agent installed. You hand it
  work; it brings its own model, tools, skills and permissions.

The second is better on almost every axis, and the deciding one is not cost.

**It deletes the failure modes.** Reasoning tokens silently consuming the completion
budget, truncated JSON, providers treating `strict: true` schemas as advisory — these are
*provider-API* problems. Agents have solved them internally.

**It removes the inference bill.** Reselling tokens at thin margin against users who can
price the raw API is a bad business. Compute the user already pays for is not.

**It makes grounding possible**, and this is the real argument. An API call receives a
sentence and nothing else, so it asks generic questions — *"how do reviewers sign in?"*.
An agent that can read the repository asks in the project's own vocabulary — *"does an
unresolved comment stop a rule reaching **agreed**?"*, *"blocks merge, like **drift**
does?"*. Both were produced from the identical prompt; the difference was repo access.

Cost, stated honestly: grounding is roughly **5× the latency** of a bare API call, and
agent-to-agent variance means a user on a weak setup gets weak questions and blames you.

## The contract lives in the prompt and the validator

You cannot force structured output from someone else's agent — no `response_format`. That
turns out not to matter, because schema enforcement was never doing the work.

Three layers, only two of which are real:

| Layer | Enforces | Verdict |
|---|---|---|
| JSON schema | field names, types, lengths | **advisory** — providers returned objects missing required fields |
| System prompt | what the fields *mean* | the actual contract |
| Your own validator | everything a schema cannot express | the enforcer |

A schema cannot say "this cost must be a real consequence", "exactly one option is
recommended", or "two options that move the plan identically mean the question should not
exist". Those are the rules that matter, so they live in code you control, and the agent
must pass them.

## Audit, then repair

The loop: agent produces output → validator checks it → violations are fed back verbatim
→ agent fixes them. One round-trip converts a broken contract into a clean result.

Better still, **move the audit rules into the prompt as a self-check** the model verifies
before replying. The repair loop then stops being the normal path and becomes a backstop.

> **Measured:** moving the rules into the prompt cut a grounded planning run from **185s to
> 94s**, because the repair round-trip stopped firing.

## What measurement showed

Worth recording, because the results were not what the prose predicted.

**Self-reported numbers are the unstable element.** Asking a model for an `impact` integer
produced wild run-to-run variance. Deriving impact from per-option task counts and sorting
in code:

| Same prompt, 3 runs | before | after |
|---|---|---|
| questions generated | 4, 8, 4 — spread 4 | 4, 4, 4 — **spread 0** |
| audit failures | 0, 1, 9 | 1, 0, 0 |
| spec-vs-code classification (weaker model) | 0/4 correct | **4/4 correct** |

Removing a field the model had to invent removed the variance. Ordering, once done in
code, became impossible to get wrong.

**Show, don't describe.** Models copy a worked example far more reliably than they follow
prose rules. The failure that prose could not fix — every option returning identical task
deltas — was fixed by one filled-in example with contrasting numbers.

**Validate your validator.** A discrimination check that compared `add + cut` as a *sum*
scored `+5/-0` and `+3/-2` as identical and rejected good questions. Comparing the pair
fixed it. A rule that rejects correct output is worse than no rule, because it trains
people to ignore the gate.

## Prior art worth reading

[Kiro][kiro] and [GitHub Spec Kit][speckit] both do spec-driven development well, and both
**cap questioning arbitrarily** — Spec Kit at five, Kiro at zero, forbidding clarification
before a first draft. Neither has a principled stopping rule, which is the gap
[impact-based termination](03-planning.md) fills.

Spec Kit's ambiguity taxonomy is worth stealing outright: scope, data model, interaction
flow, non-functional, integration, edge cases, constraints, terminology — ranked
scope-first, then security and privacy. Kiro's draft-first stance is also right: people
react better to a concrete draft than to an interrogation.

[kiro]: https://kiro.dev/docs/specs/
[speckit]: https://github.com/github/spec-kit
