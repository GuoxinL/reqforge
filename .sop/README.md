# .sop/ 骨架说明(README)

> 项目内嵌式需求整理 SOP Harness —— 纯 Markdown 规则库,不绑定任何 AI 工具。
> 版本:v0.1 | 更新:2026-08-14

## 这是什么

把"需求整理 SOP"装进项目目录的规则库。放入**已有项目**(有文档/代码)或**新目录**,用一句话需求触发,AI 按六阶段流程产出结构化 PRD,每个阶段结束等你确认(HITL)。

## 目录结构

```
.sop/
├── SOP.md                 # 主规则:加载后即成为"需求整理助手"(必读入口)
├── commands.md            # 命令参考:/sop:new /sop:req /sop:clarify /sop:prd /sop:review /sop:gate /sop:ship /sop:status
├── templates/
│   ├── PRD-模板.md        # PRD 主文档结构(9 章,FR 编号 + Given-When-Then 验收)
│   └── 视觉规格-模板.html # 视觉规格文件模板(布局/颜色/状态演示;MD 中锚点引用之)
├── checklists/
│   └── 门禁清单.md        # 05 门禁的 6 大检查项 + 打回规则
└── state/
    └── README.md          # 状态层:requirements / decisions / session / project-context 模板
.sopignore                 # 上下文扫描排除规则
AGENTS.md                  # 规则引导文件(/sop:init 自动生成或追加)
CLAUDE.md → AGENTS.md      # 软连接(Claude Code 读取)
CODEBUDDY.md → AGENTS.md   # 软连接(CodeBuddy 读取)
docs/sop/                  # 六阶段完整 DO/DON'T 规则(单源,本项目已安装)
docs/requirements/         # 需求工作目录:每需求一目录,阶段产物留存(见 SOP.md §6)
```

## 接入方式

### 方式一:AGENTS.md + 软连接(推荐,`/sop:init` 自动完成)

`/sop:init` 会按以下分支逻辑部署规则引导文件(详见 `SOP.md` §3.5):

- **已有项目**:检测到根目录 `AGENTS.md` 存在 → 在其末尾**追加**引用 `.sop/SOP.md` 的段落,不动原有内容;
- **新项目(空目录)**:新建 `AGENTS.md`,并创建 `CLAUDE.md → AGENTS.md`、`CODEBUDDY.md → AGENTS.md` 等**软连接**,让多个 AI 工具共享同一份规则。

手动执行等价操作:

```bash
# 1. 确保项目根有 AGENTS.md(已有则追加引用段,没有则新建,模板见 .sop/SOP.md 附录 A)
# 2. 创建软连接(WSL / macOS / Linux)
ln -s AGENTS.md CLAUDE.md
ln -s AGENTS.md CODEBUDDY.md
# 原生 Windows 用:mklink CLAUDE.md AGENTS.md(需开发者模式/管理员权限)
```

之后在项目目录内直接:

```
/sop:req 为登录模块增加企业微信扫码登录
```

### 方式二:Claude Code(手动,不依赖软连接)
把 `SOP.md` 内容写入项目 `CLAUDE.md`(或让 CLAUDE.md 引用 `.sop/SOP.md`),并在 Agent 配置中注册 slash commands。

### 方式三:Cursor
把 `SOP.md` 放入 `.cursor/rules/`(或用 `@.sop/SOP.md` 在对话中引用),其余命令以自然语言触发。

### 方式四:任意通用 LLM(零配置)
把 `.sop/SOP.md` 的内容粘贴进对话开头(或告诉助手"先读 .sop/SOP.md"),然后说:

```
/sop:req 一句话需求
```

## 使用流程速览

| 场景 | 第一步 | 之后 |
|---|---|---|
| 新目录 | `/sop:new 一句话需求` | `/sop:clarify` → `/sop:prd` → `/sop:review` → `/sop:gate` → `/sop:ship` |
| 已有项目 | `/sop:init`(建档,一次) | `/sop:req 一句话需求` → 同左 |
| 随时 | `/sop:status` / `/sop:help` | — |

## 与 docs/sop/ 的关系(单源)

- `docs/sop/stages/` 是六阶段 DO/DON'T 的**权威源**(人类可读的知识库)。
- `.sop/SOP.md` 内嵌每阶段**摘要规则**,完整规则指向 `docs/sop/stages/`。
- 规则演进改 `docs/sop/`,运行时由 AI 读取,避免双份维护。

## 待办 / 演进方向

- [ ] 用真实需求试跑一遍 `/sop:req → /sop:ship`,验证门禁有效性
- [ ] 确认"设计接入点"结论后,将设计轨道融入 03/04 阶段规则
- [ ] (可选)打包为 Claude Code 插件 / 独立 CLI
- [ ] (可选)沉淀为可安装的 skill,一键部署到任意项目
