# 领域文档

[English](domain.md) | 中文

工程 skill（技能）在探索代码库时应如何消费本仓库的领域文档。

## 探索前先读这些

- 仓库根部的 **`CONTEXT-MAP.md`**——它为每个 context（上下文）指向一份 `CONTEXT.md`。阅读与主题相关的每一份。context 即包分组：`packages/<group>/`（例如 `packages/core/`、`packages/llm/`）。
- **决策记录**——本仓库把持久的决策理由记录在 `.agents/notes/`（范围与归档策略见 `.agents/notes/README.md`）。阅读与你即将工作的区域相关的 Agent Note。由 `/domain-modeling` 创建的 context 级决策位于 `packages/<group>/docs/adr/`。
- 在某个 context 尚无 `CONTEXT.md` 之前，`docs/glossary.md` 与 `docs/architecture.md` 是词汇与架构的存放地。

这些文件不存在时**静默继续**。不要标记它们的缺失；也不要预先建议创建。`/domain-modeling` skill（经 `/grill-with-docs` 与 `/improve-codebase-architecture` 触达）会在术语或决策真正落定时惰性创建它们。

## 文件结构

多 context 仓库（根部存在 `CONTEXT-MAP.md`）：

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

## 使用词汇表的词汇

当你的产出命名一个领域概念（issue 标题、重构提案、假设、测试名）时，使用相关 `CONTEXT.md`（或在它存在之前用 `docs/glossary.md`）定义的术语。不要漂移到词汇表明确回避的同义词。

你需要的概念还不在词汇表里时，这是一个信号——要么你在发明项目不使用的语言（重新考虑），要么存在真实缺口（记下来交给 `/domain-modeling`）。

## 标出决策记录冲突

当你的产出与既有决策记录（`.agents/notes/` 或 context 级 ADR）矛盾时，显式指出，而不是静默覆盖：

> _与 `<decision-record path>` 矛盾——但值得重开，因为……_
