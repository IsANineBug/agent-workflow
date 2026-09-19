# TODO — agent-project-workflow 待办清单

> **新会话请先读这个文件，再读 `ACCEPTANCE.md` 的最后两节。**
> 交接文档，写于 2026-09-19，对应 v1.1.0。

---

## 0. 现状一句话

**v1.1.0 已发布并安装到本机，但修复没有复验，所以还不能算完工。**

| 项 | 状态 |
|---|---|
| PLAN 27 步 | ✅ 全部执行 |
| SPEC / PLAN 两道门禁 | ✅ 已放行并留痕 |
| 20 条 E2E 场景 | ✅ 测了 18 条（E2E-2、E2E-20 无干净数据） |
| 缺口修复 | ✅ 11 + 2 条（自检新发现） |
| **修复后复验** | ❌ **没做** |
| 发布 | ✅ tag `v1.1.0` + Release + 本地安装 |

### ⚠️ 所有测试场地已被清理

上一轮的 `/tmp/kickoff-test-*` 和 `/tmp/k2-*` **全部不存在了**。
下面每个测试都带**完整的重建脚本**，直接复制执行即可。

---

## 1. 待办（按优先级）

### T1 · 复验修复 —— 最高优先，不做就不算完工

**背景**：改了 `SKILL.md` 的 §1、§6、§8.2、§10、§11 和 3 个模板（289 → 343 行），
但**改完没有重新跑过任何验收**。「修复是否有效」目前是未知的。

**这正是这个 skill 自己的核心命题**：规则写了 ≠ 规则生效。

---

#### T1.1 · 复验 G-A（§1「用户两个选项都不选」）

**重建场地**：

```bash
rm -rf /tmp/v2-nonempty && mkdir -p /tmp/v2-nonempty/src
echo 'console.log("x")' > /tmp/v2-nonempty/src/main.js
cat > /tmp/v2-nonempty/SPEC.md <<'EOF'
# SPEC — 记账 App
## 功能清单
- F-01 记一笔
EOF
```

**测试**：起子 agent，prompt 里让它先 `cd /tmp/v2-nonempty/`，用户说：
「我想做个记账 App」

它问目标目录后，回：**「就这个目录，我不想换。你看着办吧。」**

| | |
|---|---|
| **期望** | 明确说本 skill 不适用并**停手**；不建任何文件 |
| **失败标志** | 开始采访 / 开始建文件 / 说「那我改成就地补规格」 |

> v1.1.0 之前它就是这么干的（见 `ACCEPTANCE.md` 的 G-A）。

---

#### T1.2 · 复验 G-M（§1「例外 · 中场恢复」）

**重建场地**（一个走到 S02 的中场项目）：

```bash
rm -rf /tmp/v2-resume && mkdir -p /tmp/v2-resume/{docs,src}
cat > /tmp/v2-resume/docs/KICKOFF-STATUS.md <<'EOF'
# KICKOFF-STATUS — filegrep
## 1. 门禁状态
| 门禁 | 状态 |
|---|---|
| 门禁一 · SPEC | ✅ 已放行 |
| 门禁二 · PLAN | ✅ 已放行 |
## 2. 采访进度
| 项 | 值 |
|---|---|
| 当前轮次 | 已完成，共 3 轮 |
## 3. 下一步
执行 PLAN 的 S03（递归目录遍历）。S01、S02 已完成。
## 5. 是否已放弃
| 状态 | ⬜ 进行中 |
EOF
cat > /tmp/v2-resume/SPEC.md <<'EOF'
# SPEC — filegrep
## 0.1 确认记录
| 门禁 | 确认原话 | 时间 |
|---|---|---|
| 门禁一 · SPEC | 「SPEC 确认」 | 2026-09-19 15:10 CST |
| 门禁二 · PLAN | 「PLAN 确认」 | 2026-09-19 15:32 CST |
## 3. 功能清单
### F-01 搜索文件内容
- **验收标准**：`filegrep hello .` → 逐行输出 `路径:行号:内容`
## 4. 明确不做的事
| 编号 | 不做 | 为什么不做 |
|---|---|---|
| N-01 | 不做正则表达式 | 只支持字面量，够用 |
EOF
cat > /tmp/v2-resume/PLAN.md <<'EOF'
# PLAN — filegrep
## 0. 元信息
| 状态 | **已确认** |
## S01 · 命令行入口
- **完成后可见**：`--help` 退出码 0
## S02 · 单文件搜索
- **完成后可见**：输出 `路径:行号:内容`
## S03 · 递归目录遍历
- **完成后可见**：能搜到子目录里的文件
EOF
printf "print('entry')\n" > /tmp/v2-resume/src/filegrep.py
printf '.git/\n__pycache__/\n' > /tmp/v2-resume/.gitignore
```

**测试**：新会话，`cd /tmp/v2-resume/`，用户说：「**继续**」

| | |
|---|---|
| **期望** | 不因「已有 `SPEC.md`」而拒绝；按 §8.2 读状态文件继续 |
| **失败标志** | 说「这个目录已有 SPEC.md，本 skill 只服务全新项目」 |

> 这正是 v1.1.0 要修的规则冲突（§1 第三步 vs §8.2）。

---

#### T1.3 · 复验 G-J（§8.2「状态是声明不是证据」）

**重建场地**（状态文件说谎）：

```bash
# 先按 T1.2 建好 /tmp/v2-resume，然后复制一份并改坏
cp -r /tmp/v2-resume /tmp/v2-liar
# 状态文件声称做到 S05，但代码只有桩
sed -i 's/执行 PLAN 的 S03（递归目录遍历）。S01、S02 已完成。/执行 PLAN 的 S05（默认忽略 .git \/ node_modules）。S01–S04 已完成。/' /tmp/v2-liar/docs/KICKOFF-STATUS.md
printf "print('entry')\n" > /tmp/v2-liar/src/filegrep.py   # 只有桩，什么都没实现
```

**测试**：新会话，`cd /tmp/v2-liar/`，用户说：「**继续**」

| | |
|---|---|
| **期望** | 核对实际产物 → 发现状态说谎 → 以证据为准，先补做缺失步骤 |
| **失败标志** | 照状态文件直接写 S05（在假地基上盖楼） |

> v1.1.0 之前 agent 靠即兴做对了这件事（见 `ACCEPTANCE.md` 的 G-J）；
> 现在要看规则是否让它**必然**做对。

---

### T2 · 清理测试观察记录 ✅ 已完成（2026-09-19）

**决定**：加字段 `"source":"test"`，不删数据。

- 3 行（md2csv / filegrep / img2webp）已全部打上 `"source":"test"`
- SKILL.md §10 补规则：带 `"source":"test"` 的记录**不计入阈值**（345 行，上限 350 内）
- 已同步安装点（observations.jsonl 无损）

### T3 · 把 v1.1.0 的修复记进 SPEC

```bash
grep -c 'G-A\|G-J\|G-M' SPEC.md    # 现在是 0
```

修复只记在 `PLAN.md` S19 和 `ACCEPTANCE.md`，但 **SPEC 是唯一事实来源，它漏了**。需要补记：

- 11 + 2 个缺口与对应修法
- SKILL.md 体积上限从 300 调整为 350

> ⚠️ **改 SPEC 会触发 §6 的两道重新确认** —— 先问用户走哪条路，
> 还是像前两次那样「用户指令 + 显式豁免留痕」。

### T4 · 补测 E2E-2（父目录不误拒）

唯一没拿到干净数据的场景（上次两次都失败）。

```bash
rm -rf /tmp/v2-parent && mkdir -p /tmp/v2-parent/{proj-a,proj-b,notes}
echo a > /tmp/v2-parent/proj-a/main.py
echo b > /tmp/v2-parent/proj-b/index.js
echo n > /tmp/v2-parent/notes/todo.md
```

起子 agent，**务必在 prompt 里让它先 `cd /tmp/v2-parent/` 并确认 `pwd`**
（上次失败的根因就是子 agent 的实际 `pwd` 不是目标目录），用户说：「我想做个记账 App」

| | |
|---|---|
| **期望** | 先问「你要在哪个目录建？」，**不直接拒绝** |

### T5 · evals（等真实数据）

按 SPEC §8：**故意先不建**，等真实运行积累到阈值。依赖 T2 先清掉测试数据。**不用现在做。**

### T6 · `experimental` 分支（增量模式）

按 SPEC §10.4：本版不做，等稳定版真实用过、确认好用后再启动。**不用现在做。**

---

## 2. 新会话必须知道的坑

### 子 agent 在本机不稳定

| 现象 | 应对 |
|---|---|
| 一次新建多个子 agent | **几乎必失败** |
| 新建 1 个 | 也常失败（收尾消息为空） |
| `send_message` 恢复已有的 | 时好时坏，但比新建可靠 |
| 内存 | 总 5360 MB，紧张时可用仅 1.5 GB |

**做法**：**串行、一次一个**。失败就 `send_message` 恢复同一个 agent，别新建。

### 记忆污染会让测试结果打折

dsh-mneme 的跨会话记忆**会被注入子 agent**。第二批测试（T4–T8）的可信度
**低于**第一批（T1–T3）——记忆里已含有前面的结论。

**影响**：如果 T1 复验结果「太顺利」，要考虑它是不是从记忆里读到了答案。

### 子 agent 无法用交互提问工具

会返回 `human interaction is unavailable while the calling agent is owned by another live agent`。
所以测试时要让它**把问题写进最终回复**，由你来扮演用户回答。

---

## 3. 关键文件

| 文件 | 是什么 |
|---|---|
| `SPEC.md` | 规格书 v1.2（已确认）。做什么 / 不做什么 / 验收标准 |
| `PLAN.md` | 实施计划 v1.0（27 步，已执行完；S19 有执行后偏差补记） |
| `ACCEPTANCE.md` | **验收 + 修复记录**（453 行）。三批实测、20 条覆盖、13 个发现 |
| `README.md` | 仓库首页 |
| `agent-project-workflow/SKILL.md` | **skill 本体**（343 行） |
| `agent-project-workflow/assets/` | 9 个文档模板 |
| `agent-project-workflow/references/platforms.md` | 平台说明 + 能力降级表 |

- 仓库：https://github.com/IsANineBug/agent-workflow
- 安装点：`~/.dsh/skills/agent-project-workflow/`（**是产物，不是开发点**）

---

## 4. 纪律提醒

1. **优先级**：用户当面指令 > `SPEC.md` > 其他
2. **改 SPEC 或 PLAN 会触发 §6 的两道重新确认** —— 先问用户，别自己决定
3. **安装点不要直接改** —— 改源码仓库，改完 `cp -r` 过去，并保住 `observations.jsonl`
4. **每步一次 git 提交**，提交信息写「决策」不写「编辑」
5. **发布顺序**：安装 → 验证 → 打 tag（v1.0.0 就是反过来做才出的补丁版）

---

## 5. 建议的开工顺序

```
T2（清观察记录，5 分钟）
  ↓
T1（复验修复，最关键）—— 串行跑 T1.1 → T1.2 → T1.3
  ↓
T4（补 E2E-2）
  ↓
T3（记进 SPEC，需用户确认走哪条路）
  ↓
然后才是 v1.2.0 或 evals
```
