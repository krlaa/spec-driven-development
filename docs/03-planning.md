# Planning

Planning is where a rough idea becomes rules. The hard parts are knowing which questions
are worth asking, when an agent may guess, and when to stop.

## Questions are first-class

A question in a chat transcript is lost the moment you scroll. A question here is an
artifact with an id, an attachment point, and a lifecycle — because **a question is what a
rule looks like before it exists.** Answering it does not produce a reply; it produces
spec content.

This dissolves the blank-page problem. You do not author a spec. You answer one into
existence.

## Blocking is mechanically decidable

Agents hit ambiguity constantly. Blocking on all of it wastes hours; guessing at all of it
builds the wrong thing. The discriminator is one question:

> **Does answering this edit the spec, or only the code?**

- **Edits the spec** — what to build, intent. Only the human knows. **Block.** It is cheap
  now and expensive later, and there is nothing else to do meanwhile: the human *is* the
  work at this stage.
- **Edits only the code** — how to build it. Reversible, local, and a test will catch a
  wrong guess. **Proceed on a stated assumption**, and record it as one.

Note this is the same boundary as [code-only contracts](02-the-contract.md). One line
doing two jobs is usually a sign the line is real.

A useful consequence: **the spec is a cache of answers, and its coverage is what licenses
autonomy.** Blocking is front-loaded so that it can stop. Every question answered during
planning is one no agent has to ask at 3am. That gives a claim worth making — *"spec
coverage 80% → agents run unattended on 80% of the work"* — which is the argument for
sitting through planning at all.

Assumptions, when an agent does proceed, should be a visible state below `agreed`, and
should **block `verified` no matter how many tests pass.** A spec that can report "12 of
41 rules are assumptions no human confirmed" is telling you something no other tool does.

## Questioning must terminate

If planning blocks, it has to end, or people skip it. Every tool has this problem and most
answer it arbitrarily — a cap of three or five questions, or "stop when the human gets
bored".

A better rule: **stop when the next question cannot change the task list.**

Generate the plan continuously during planning. A question earns its place only if
answering it differently produces different tasks. When no open question changes the plan,
planning is done — mechanically, not by feel.

This gives planning a progress bar, which is what makes blocking tolerable. Three
mechanics make it work:

1. **State the impact before the answer.** "Answering this changes the plan by 3 tasks."
   The reader knows whether to think hard.
2. **Show the delta after.** "+3 tasks · 4 questions left that can still change it." The
   answer visibly moved the artifact.
3. **Let answers drain other questions.** Settling one often implies others; those lose
   impact and demote themselves to assumptions. The queue shrinks faster than you answer.

**Impact must be derived, never self-reported.** Ask a model for an "impact 1–10" and it
invents a number that does not agree with its own task estimates. Have it estimate
concrete task counts per option, and compute impact from the spread. See
[agents](04-agents.md) for what that measurement showed.

## Every option carries its cost

A question should be answerable with one tap, which means the recommended answer has to be
genuinely defensible. Each option gets three things:

- the answer, in plain language
- the case for it, one sentence
- **what it costs you**, one sentence, concrete

The cost line is what makes one-tap honest instead of a rubber stamp. An option with no
real cost is not a real option — and a model asked for one will happily invent "none",
which is worth rejecting automatically.

## Two escapes, never one

"I know the answer, it's just not listed" and "I don't know yet" are different intents.
One button for both means every typed answer risks becoming a tangent, and every tangent
risks being recorded as a decision.

- **Something else** → free text, recorded verbatim, queue advances.
- **Talk it through** → a side thread that *holds the queue's place* and exits by settling
  on an answer.

The second one pays off a debt. Atomising a spec kills narrative — "why did we decide
this" has nowhere to live. It does now: **the side conversation becomes the rule's
recorded reason.** Rationale stops being a field nobody fills in and becomes the
by-product of having been unsure.
