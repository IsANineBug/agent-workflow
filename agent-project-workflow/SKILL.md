---
name: agent-project-workflow
description: 在写任何代码之前，先采访用户澄清需求，产出 SPEC.md / PLAN.md / AGENTS.md / INDEX.md / docs/ 全套上下文工程文档，并用硬门禁保证 SPEC 与 PLAN 未确认前不写任何源码。Kick off a brand-new project the right way — interview the user first, then produce a SPEC/PLAN/AGENTS.md/INDEX.md/docs set, with a hard gate that blocks all source code until SPEC and PLAN are confirmed. Use this whenever the user says "我想做个 X"、"帮我开个新项目"、"搭个架子"、"从零开始做一个项目", or "start a new project"、"kick off a new project"、"bootstrap a new repo"、"build X from scratch" — even if they don't explicitly ask for a spec or a plan. 只用于**项目级**的全新项目；不用于给现有项目加模块、重构、修 bug，也不用于"我想做个函数/按钮/脚本"这类单点修改。
---

# Agent Project Workflow

## 0. 这个 skill 做什么

让 agent 在**任何全新项目动手写代码之前**，先走完「采访 → SPEC → PLAN → 上下文工程文档体系」。

它交付的不是代码，是**一份给未来 agent 的说明书**。所以它要解决的核心问题不是「文档写得漂不漂亮」，而是 **「agent 读了但没照做」**。

### 该触发

| 用户说 | 英文对应 |
|---|---|
| 「我想做个 X」（X 是**项目级产物**） | "start a new project" |
| 「帮我开个新项目 / 搭个架子」 | "kick off a new project" / "bootstrap a new repo" |
| 「从零开始做一个 X」 | "build X from scratch" |

### 不该触发

| 用户说 | 为什么 |
|---|---|
| 「给现有项目加个大模块」 | 本 skill 只服务全新项目，会覆盖已有文档 |
| 「重构一下这个项目」 | 改现有代码，流程不同 |
| 「修个 bug / 改个函数」 | 单点修改不需要整套流程 |
| 「我想做个函数 / 做个按钮 / 写段脚本」 | 含「我想做个」但**不是项目级产物** |

---

## 1. 前置检查（进入流程前必做）

**顺序不能颠倒。**

**第一步 · 确认目标目录。** 不要假定当前工作目录就是目标目录。
用户在 `~/work/`（里面一堆项目）说「我想做个记账 App」，本意可能是在 `~/work/bookkeeping/` 新建 —— 此时直接拒绝就是**拒错了**。先问「你要在哪个目录建？」。

**第二步 · 判定是否全新。** 目录为空，或只含 `.git/`、`README.md`、`LICENSE`、`.gitignore` 等无害文件 → 视为全新。

**第三步 · 已有文件就停。** 若目标目录里有源码、构建配置，或已有的 `SPEC.md` / `PLAN.md` / `AGENTS.md` → **停止流程**，说明本 skill 只服务全新项目，并给出两个选项：换一个空目录 / 放弃本流程。

任何创建动作都发生在第三步通过之后。
