# ReqForge — 需求整理 SOP Harness

把"原始需求 → 结构化需求文档(PRD)"的完整流程,以**纯 Markdown 规则库**的形式装进任意项目:六阶段(采集 → 澄清 → 结构化 → 评审 → 门禁 → 交付),每阶段有明确的"该做什么/不该做什么",产出物经**人工评审**通过后才进入下一阶段。不绑定任何 AI 工具。

> 仓库:`https://github.com/GuoxinL/reqforge`

## 快速开始(最快:复制提示词,零门槛)

复制下方提示词(仓库地址已内置),粘贴到你的 agent(需支持执行 `git clone` 与文件操作,如 Claude Code / Cursor / CodeBuddy / WorkBuddy)对话中执行——**它会自动 clone 本仓库 → 部署规则到当前项目 → 清理临时目录,执行完即安装好**:

```text
你是安装助手。请把「需求整理 SOP Harness」安装到当前项目,按以下步骤执行:
1. git clone https://github.com/GuoxinL/reqforge.git /tmp/sop-harness-install
2. 将 /tmp/sop-harness-install/.sop/ 整个目录复制为当前项目根目录 .sop/(含 stages/ 六阶段规则)
3. 接入 AGENTS.md:
   - 已存在:末尾追加(保留原内容,已有 "SOP Harness 引用段" 标记则跳过):
     <!-- SOP Harness 引用段 --> 本项目的需求整理流程由 SOP Harness 管理。开始需求工作前先阅读 .sop/SOP.md,按其中命令(/sop:new|req|clarify|prd|review|gate|ship|status)执行;与本文件其他规则冲突时以 .sop/SOP.md 为准。
   - 不存在:新建 AGENTS.md,内容:# AGENTS.md —— 本项目的 AI 协作与需求整理规则由 SOP Harness 管理。主规则:.sop/SOP.md;命令:.sop/commands.md;收到需求任务时按 .sop/SOP.md 执行,每个阶段结束等待用户确认。
4. 删除临时目录 /tmp/sop-harness-install
完成后列出已创建/修改的文件清单,并提示用户可用 /sop:req 一句话提需求。
```

安装完成后直接:

```
/sop:req 为登录模块增加企业微信扫码登录
```

## 快速开始(WorkBuddy:一句自然语言安装)

clone 本仓库后,在 WorkBuddy 中打开本项目,直接说:

> **"安装需求整理 SOP Harness"** 或 **"初始化需求整理流程"**

项目级技能位于 `.workbuddy/skills/req-sop-harness/`(随仓库自动分发,clone 即可用)。触发后自动完成:

1. 复制 `.sop/` 规则库(主规则 / 命令 / PRD 模板 / 视觉规格模板 / 门禁清单 / 状态层);
2. 处理 `AGENTS.md`——已有项目:**追加**引用段(保留原内容);新项目:**新建** + 软连接 `CLAUDE.md`、`CODEBUDDY.md`;
3. 建立 `docs/requirements/` 需求目录规范。

> 不用 WorkBuddy 也可以:把 `.sop/` 复制到目标项目根,让 AI 先读 `.sop/SOP.md` 即可(兼容 Claude Code / Cursor / 任意 LLM)。

## 使用(一句话提需求)

```
/sop:req 为登录模块增加企业微信扫码登录
```

自动创建需求目录 `docs/requirements/20260814-企业微信扫码登录/`,随后逐阶段推进——每个阶段产物留档、停下等你确认:

| 命令 | 作用 |
|---|---|
| `/sop:init` | 已有项目初始化(扫描上下文 + 部署规则文件) |
| `/sop:new` / `/sop:req` | 一句话提需求(新目录 / 已有项目) |
| `/sop:clarify` → `/sop:prd` → `/sop:review` → `/sop:gate` → `/sop:ship` | 六阶段推进 |
| `/sop:status` / `/sop:help` | 状态查询 / 帮助 |

## 目录结构

```
├── .sop/                        # ① 规则库(安装的核心,自包含)
│   ├── SOP.md                   #    主规则:命令速查 / 六阶段流程 / HITL / 状态管理 / 输出约定(必读入口)
│   ├── commands.md              #    命令参考:/sop:new|init|req|clarify|prd|review|gate|ship|status
│   ├── README.md                #    .sop/ 骨架说明与接入方式
│   ├── stages/                  #    六阶段 DO/DON'T(权威规则源)
│   │   ├── README.md            #      阶段总览与索引
│   │   └── 01-需求采集.md
│   │       ~ 06-交付集成.md      #      每阶段:目标 / 输入 / 该做什么 / 不该做什么 / 产出 / 门禁
│   ├── templates/               #    输出模板
│   │   ├── PRD-模板.md          #      PRD 主文档结构(9 章,FR 编号 + Given-When-Then 验收)
│   │   └── 视觉规格-模板.html    #      视觉规格(布局/颜色/状态演示)
│   ├── checklists/
│   │   └── 门禁清单.md          #    05 门禁的 7 项检查 + 打回规则
│   └── state/                   #    状态层(模板随仓库;运行时文件被 git 忽略)
│       └── README.md            #      requirements 索引 / decisions 决策 / session 进度 / project-context 档案
├── docs/
│   ├── requirements/            # ② 需求工作目录(运行时生成):每需求一个目录,阶段产物全留存
│   │   └── <YYYYMMDD>-<需求名>/ #      01~06 阶段文件 + 视觉规格 HTML
│   ├── examples/                # ③ 示例产物:试跑 PRD + 视觉规格 HTML(浏览器可直接打开)
│   └── research/                # ④ 项目调研报告(市场调研 / 选型依据)
├── .workbuddy/skills/           # ⑤ 项目级技能(随仓库分发,WorkBuddy 里一句话即可触发)
├── .sopignore                   # ⑥ 上下文扫描排除规则(AI 扫描项目时忽略的目录/文件)
├── .gitignore                   # ⑦ git 忽略规则(agent 配置 / 本地数据 / 运行时状态不进仓库)
├── LICENSE                      # ⑧ MIT 许可证
└── README.md                    # 本文件
```

### 各目录/文件的意义

| 路径 | 角色 | 说明 |
|---|---|---|
| `.sop/` | **规则库(安装核心)** | 安装后全部规则都在这里,自包含、纯 Markdown、随项目走;换 agent / 换机器不丢 |
| `.sop/SOP.md` | 主规则 | AI 加载后即按六阶段执行需求整理;定义命令、HITL、状态管理、输出约定 |
| `.sop/stages/` | 阶段规则 | 六阶段"该做什么/不该做什么"的完整权威源,`SOP.md` 引用它 |
| `.sop/templates/` | 输出模板 | PRD 主文档结构与视觉规格 HTML 模板,`/sop:prd` 强制遵循 |
| `.sop/checklists/` | 门禁清单 | `/sop:gate` 执行的 7 项质量检查,不过关打回对应阶段 |
| `.sop/state/` | 状态层 | 跨会话记忆:需求索引 / 决策 / 进度 / 项目档案(运行时生成) |
| `docs/requirements/` | 需求资产 | 每个需求一个目录,阶段产物 01~06 全留存、不删除(团队共享) |
| `docs/examples/` | 示例 | 试跑产生的 PRD 与视觉规格,可直接打开体验产出形态 |
| `docs/research/` | 调研 | 项目立项时的市场调研与选型报告 |
| `.workbuddy/skills/` | 技能 | WorkBuddy 项目级技能,clone 后"一句话安装";与复制提示词安装等价 |
| `.sopignore` | 扫描排除 | `/sop:init`、`/sop:req` 扫描项目上下文时忽略的目录/文件 |
| `.gitignore` | git 排除 | agent 配置(`.codebuddy/`、`.claude/`)、本地缓存、运行时状态不进仓库 |

## 产物形态

- 每个新需求一个目录 `docs/requirements/<YYYYMMDD>-<需求名>/`;
- 阶段产物按 `<阶段数字>-<阶段名称>.md` 留存,**不删除、不覆盖**(回退重做追加 `-v2`);
- 涉及页面布局/颜色/样式的功能:MD 只写行为 + 锚点引用,视觉规格用独立 HTML(`视觉规格-<模块>.html`)承载,评审所见即所得;
- 完整产出示例见 `docs/examples/`(含视觉规格 HTML,可直接浏览器打开体验)。

## 说明与注意事项

- **纯 Markdown**,零脚本依赖,可在任何支持读文件的 AI 工具中使用;
- `AGENTS.md` 软连接在 **Windows 上 clone** 需要 git 配置 `core.symlinks=true`,否则会退化为普通文件(可手动重建软连接);
- 规则演进:修改 `.sop/`(含 `stages/`)后,同步更新 `.workbuddy/skills/req-sop-harness/` 技能包;
- 许可证:见仓库 LICENSE(如适用)。
