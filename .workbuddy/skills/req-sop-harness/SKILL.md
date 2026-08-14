---
name: req-sop-harness
description: 项目内嵌式"需求整理 SOP Harness":把原始需求按六阶段(采集→澄清→结构化→评审→门禁→交付)整理成结构化需求文档(PRD),每个阶段产出物经人工评审通过后才进入下一阶段。当用户需要初始化需求整理流程、部署 .sop/ 规则库、一句话提需求(/sop:new /sop:req)、澄清需求、生成 PRD(含视觉规格 HTML)、评审需求文档或查询需求状态(/sop:status)时使用。支持已有项目(扫描上下文 + AGENTS.md 追加引用)与新项目(建目录 + 软连接)。纯 Markdown 规则,不绑定任何 AI 工具。
agent_created: true
---

# 需求整理 SOP Harness

## 用途

把"把需求整理成需求文档"的标准作业流程(SOP)封装为可部署到任意项目目录的规则库:六阶段流程 + 每阶段"该做什么/不该做什么" + 产物模板 + 人工评审门禁。AI 按规则执行,人在每个阶段边界拍板(HITL)。

## 何时使用

- 用户说"初始化需求整理流程 / 部署需求 SOP / 把这个项目接入需求整理"
- 用户要写 PRD、需求文档、澄清需求、评审需求文档
- 用户输入:`/sop:new` `/sop:req` `/sop:init` `/sop:clarify` `/sop:prd` `/sop:review` `/sop:gate` `/sop:ship` `/sop:status` `/sop:help`
- 用户提供一句话需求,希望按固定流程产出结构化文档

## 部署流程(初始化)

按以下步骤在目标项目内初始化(注意:不删除用户已有内容):

1. **复制规则库**:将 `assets/dot-sop/` 的全部内容(含 `stages/` 六阶段规则)复制为项目根 `.sop/`。
2. **处理 AGENTS.md**(分支):
   - 项目根已存在 `AGENTS.md`(已有项目)→ 在其末尾**追加**"SOP Harness 引用段"(模板见 `.sop/SOP.md` 附录 A),保留原内容;已存在引用则跳过。
   - 不存在(新项目/空目录)→ 新建 `AGENTS.md`(引导内容见附录 A),并创建软连接:`CLAUDE.md → AGENTS.md`、`CODEBUDDY.md → AGENTS.md`;其他工具规则文件按需追加。
   - 软连接命令:WSL/macOS/Linux 用 `ln -s`;原生 Windows 用 `mklink`(需开发者模式/管理员权限);git 仓库需 `core.symlinks=true`。
3. **创建需求目录规范**:`docs/requirements/<YYYYMMDD>-<需求名>/`,每个新需求一个目录。
4. 输出**变更清单**(新增/修改/软连接)供用户确认后再继续。

## 使用方式(核心流程)

1. **已有项目**:先执行 `/sop:init`(扫描项目 → 建 `state/project-context.md` → 部署上述文件);之后每条需求用 `/sop:req 一句话需求`(自动定位相关代码/文档,直入 02 澄清)。
2. **新项目/全新需求**:`/sop:new 一句话需求` 从 01 采集开始。
3. 逐阶段推进,产物写入需求目录 `<阶段数字>-<阶段名称>.md`(01-需求采集 / 02-需求澄清 / 03-文档结构化(PRD 主文档)/ 04-多方评审 / 05-质量门禁 / 06-交付集成),**不删除、不覆盖历史**,回退重做追加 `-v2`。
4. **每阶段结束必须停下等用户确认**(HITL);门禁不通过打回对应阶段,不得静默放行。
5. **视觉规格**:涉及页面布局/颜色/样式的功能,MD 只写行为 + 锚点引用,视觉细节生成独立 HTML(`assets/dot-sop/templates/视觉规格-模板.html`),与阶段产物同目录。
6. 状态管理:`.sop/state/`(`requirements.md` 索引 / `decisions.md` 决策 / `session.md` 进度 / `project-context.md` 项目档案)。

## 命令速查

| 场景 | 命令 | 作用 |
|---|---|---|
| 新需求 | `/sop:new 一句话需求` | 建需求目录,01 采集 |
| 已有项目初始化 | `/sop:init` | 扫描 + 部署规则文件(一次) |
| 已有项目新需求 | `/sop:req 一句话需求` | 直入 02 澄清 |
| 阶段推进 | `/sop:clarify` `/sop:prd` `/sop:review` `/sop:gate` `/sop:ship` | 02~06 |
| 查询/帮助 | `/sop:status` `/sop:help` | — |

## 资源清单

- `assets/dot-sop/SOP.md` — 主规则(命令速查/六阶段摘要/HITL 硬规则/状态管理/输出约定/AGENTS.md 引导模板)
- `assets/dot-sop/commands.md` — 命令详细参考
- `assets/dot-sop/templates/PRD-模板.md` — PRD 主文档结构(9 章,FR 编号 + Given-When-Then 验收,含视觉规格引用规范)
- `assets/dot-sop/templates/视觉规格-模板.html` — 视觉规格文件模板(设计令牌/色板/布局/状态/间距)
- `assets/dot-sop/checklists/门禁清单.md` — 05 门禁 7 大检查项 + 打回规则
- `assets/dot-sop/state/README.md` — 状态层说明与字段模板
- `assets/dot-sop/.sopignore` — 上下文扫描排除规则
- `assets/dot-sop/stages/01~06-*.md` — 六阶段完整 DO/DON'T 规则

## 注意事项

- 只追加不覆盖:AGENTS.md 已有内容、state 历史记录一律保留。
- 纯 Markdown 规则,可在 Claude Code / Cursor / 任意通用 LLM 中使用;不引入任何脚本依赖。
- 规则演进时同步更新本技能包(`assets/`),已部署项目按需重新复制。
