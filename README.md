# Spec-driven development

Notes on a way of building software where a written specification is the source of
authority, and the link between a rule and the code that satisfies it is mechanically
checked rather than remembered.

This repository is **concepts only**. No framework, no product, no client code.

## The problem it addresses

Specs rot. They are written once, agreed, and then diverge from the code silently. Nobody
notices, because nothing checks. After a while the spec is a historical document that
misleads anyone who reads it, and the honest response is to stop reading it — at which
point writing it was waste.

Spec-driven development is the attempt to make that divergence *visible and mechanical*.

## The shape of it

```
idea → questions → rules → tasks → code → tests → state
                     ↑                              │
                     └──────── drift ───────────────┘
```

1. **Questions** turn a rough idea into decisions. They block only when the answer
   changes *what* gets built.
2. **Rules** are the settled decisions, written twice — once plainly, once precisely.
3. **Tasks** come from rules. No task exists without a rule that asked for it.
4. **Tests** name the rule they satisfy. That name is the entire binding.
5. **State** is derived from the evidence, never stored and never hand-maintained.

## The five ideas that carry it

| | |
|---|---|
| [Atoms and registers](docs/01-the-model.md) | A spec is addressable units, each written in two registers: plain to read, precise to contract. |
| [The contract](docs/02-the-contract.md) | The test **is** the anchor. State is derived from evidence. Code only. |
| [Planning](docs/03-planning.md) | Questions are first-class. Blocking is mechanically decidable. Questioning terminates. |
| [Agents](docs/04-agents.md) | Bring your own agent. The contract lives in the prompt and the validator, not the schema. |
| [File format](docs/05-file-format.md) | Markdown in the repo. Markdown was never the problem — markdown as the *interface* was. |

## What this is not

- Not a methodology with ceremonies. There are no meetings here.
- Not a documentation practice. A document nothing checks is not part of this.
- Not a way to make an LLM write more code. It is a way to make it build less of the
  wrong thing.

See [HANDOFF.md](HANDOFF.md) for what is settled, what is open, and what was measured
rather than assumed.
