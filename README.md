# agent-workflow

按上下文工程标准规范 agent 工作流程的 skill 集合。

**当前只有一个 skill**：`agent-project-workflow` —— 在动手写代码之前，先采访澄清需求，产出 SPEC / PLAN / AGENTS.md / INDEX.md / docs 全套文档，并用硬门禁保证未经确认不写源码。

---

## 安装

```bash
git clone https://github.com/IsANineBug/agent-workflow.git
cp -r agent-workflow/agent-project-workflow ~/.dsh/skills/
```

`~/.dsh/skills/` 是 DSH 的 skill 目录。其他平台的目录见 `agent-project-workflow/references/platforms.md`（**目前只验证过 DSH**）。

---

## 怎么触发

对 agent 说：

- 「我想做个 X」（X 是**项目级**产物）
- 「帮我开个新项目 / 搭个架子」
- "start a new project" / "kick off a new project"

**不该触发**：给现有项目加模块、重构、修 bug、以及「我想做个函数 / 按钮 / 脚本」这类单点修改。本 skill 只服务**从零开始的全新项目**。

---

## 它会做什么

1. **采访** —— 每轮 3–5 问，最多 5 轮，选项式提问，每轮复述确认
2. **SPEC.md** —— 功能清单、明确不做的事、验收标准
3. **PLAN.md** —— 每步只做一件事，写清完成后能看到什么
4. **AGENTS.md / INDEX.md / docs/** —— 常驻规则、入口索引、五份细则
5. **两道硬门禁** —— SPEC 未确认不写 PLAN；PLAN 未确认不写源码

---

## 仓库结构

```
.
├── SPEC.md      ← 本 skill 的规格书（唯一事实来源：做什么、不做什么、验收标准）
├── PLAN.md      ← 实施计划（27 步，每步一次提交）
└── agent-project-workflow/
    ├── SKILL.md
    ├── assets/      ← 9 个文档模板
    └── references/  ← platforms.md（按需加载）
```

---

## 文档导航

| 想知道 | 去哪 |
|---|---|
| 这个 skill 做什么、不做什么、怎么验收 | [`SPEC.md`](SPEC.md) |
| 做到哪一步了、每步做什么 | [`PLAN.md`](PLAN.md) |
| 怎么用这个 skill（给 agent 看的指令） | [`agent-project-workflow/SKILL.md`](agent-project-workflow/SKILL.md) |
| 支持哪些平台 | [`agent-project-workflow/references/platforms.md`](agent-project-workflow/references/platforms.md) |

---

## 已知限制

- **硬门禁不是技术强制** —— 它是一份指令，agent 愿意遵守才有效
- **产出文档固定中文** —— 英文用户可以触发，但拿到的是中文文档
- **只服务全新项目** —— 给现有项目做增量变更的能力在 `experimental` 分支，尚未开始

细节见 `SPEC.md` 的 §5.8 与 §9.5。
