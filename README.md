# ReqForge — 需求整理 SOP Harness

把"原始需求 → 结构化需求文档(PRD)"的完整流程,以**纯 Markdown 规则库**的形式装进任意项目:六阶段(采集 → 澄清 → 结构化 → 评审 → 门禁 → 交付),每阶段有明确的"该做什么/不该做什么",产出物经**人工评审**通过后才进入下一阶段。不绑定任何 AI 工具。

> 仓库:`https://github.com/GuoxinL/reqforge`

## 快速开始(最快:复制提示词,零门槛)

复制下方提示词(仓库地址已内置),粘贴到你的 agent(需支持执行 `git clone` 与文件操作,如 Claude Code / Cursor / CodeBuddy / WorkBuddy)对话中执行——**它会自动 clone 本仓库 → 部署规则到当前项目 → 清理临时目录,执行完即安装好**:

```text
你是安装助手。请把「需求整理 SOP Harness」安装到当前项目,按以下步骤执行:
1. git clone https://github.com/GuoxinL/reqforge.git /tmp/sop-harness-install
2. 将 /tmp/sop-harness-install/.sop/ 复制为当前项目根目录 .sop/;若当前项目没有 docs/sop/,将 /tmp/sop-harness-install/docs/sop/ 复制为当前项目的 docs/sop/
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
├── .sop/                    # 运行时规则库(SOP.md 主规则 / commands / templates / checklists / state)
├── docs/
│   ├── sop/                 # 六阶段完整 DO/DON'T 规则(权威源)
│   ├── requirements/        # 需求工作目录:<日期>-<需求名>/ 下 01~06 阶段产物全留存
│   ├── examples/            # 示例产物(试跑 PRD + 视觉规格 HTML)
│   └── research/            # 项目调研报告
├── .workbuddy/skills/       # 项目级技能(随仓库分发,克隆即用)
├── skills-dist/             # 技能分发包(zip,可手动安装)
└── .gitignore
```

## 产物形态

- 每个新需求一个目录 `docs/requirements/<YYYYMMDD>-<需求名>/`;
- 阶段产物按 `<阶段数字>-<阶段名称>.md` 留存,**不删除、不覆盖**(回退重做追加 `-v2`);
- 涉及页面布局/颜色/样式的功能:MD 只写行为 + 锚点引用,视觉规格用独立 HTML(`视觉规格-<模块>.html`)承载,评审所见即所得;
- 完整产出示例见 `docs/examples/`(含视觉规格 HTML,可直接浏览器打开体验)。

## 说明与注意事项

- **纯 Markdown**,零脚本依赖,可在任何支持读文件的 AI 工具中使用;
- `AGENTS.md` 软连接在 **Windows 上 clone** 需要 git 配置 `core.symlinks=true`,否则会退化为普通文件(可手动重建软连接);
- 规则演进:修改 `docs/sop/` 与 `.sop/` 后,同步更新 `.workbuddy/skills/req-sop-harness/` 技能包,再重新打包 `skills-dist/req-sop-harness.zip`;
- 许可证:见仓库 LICENSE(如适用)。
