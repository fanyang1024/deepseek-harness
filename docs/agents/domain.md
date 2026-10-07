# Domain Docs

English | [中文](domain.zh.md)

How the engineering skills should consume this repo's domain documentation when exploring the codebase.

## Before exploring, read these

- **`CONTEXT-MAP.md`** at the repo root — it points at one `CONTEXT.md` per context. Read each one relevant to the topic. Contexts are package groups: `packages/<group>/` (e.g. `packages/core/`, `packages/llm/`).
- **Decision records** — this repo records durable decision rationale in `.agents/notes/` (see `.agents/notes/README.md` for scope and archive policy). Read notes that touch the area you're about to work in. Context-scoped decisions, once `/domain-modeling` creates them, live at `packages/<group>/docs/adr/`.
- Until a `CONTEXT.md` exists for a context, `docs/glossary.md` and `docs/architecture.md` are the vocabulary and architecture homes.

If any of these files don't exist, **proceed silently**. Don't flag their absence; don't suggest creating them upfront. The `/domain-modeling` skill (reached via `/grill-with-docs` and `/improve-codebase-architecture`) creates them lazily when terms or decisions actually get resolved.

## File structure

Multi-context repo (presence of `CONTEXT-MAP.md` at the root):

```
/
├── CONTEXT-MAP.md
├── .agents/notes/                     ← system-wide decision records
├── packages/
│   ├── core/
│   │   ├── CONTEXT.md
│   │   └── docs/adr/                  ← context-specific decisions
│   └── llm/
│       ├── CONTEXT.md
│       └── docs/adr/
└── docs/
    ├── glossary.md                    ← existing vocabulary home
    └── architecture.md                ← existing architecture home
```

## Use the glossary's vocabulary

When your output names a domain concept (in an issue title, a refactor proposal, a hypothesis, a test name), use the term as defined in the relevant `CONTEXT.md` (or `docs/glossary.md` until one exists). Don't drift to synonyms the glossary explicitly avoids.

If the concept you need isn't in the glossary yet, that's a signal — either you're inventing language the project doesn't use (reconsider) or there's a real gap (note it for `/domain-modeling`).

## Flag decision-record conflicts

If your output contradicts an existing decision record (`.agents/notes/` or a context ADR), surface it explicitly rather than silently overriding:

> _Contradicts `<decision-record path>` — but worth reopening because…_
