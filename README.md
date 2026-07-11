# Game Roles — 独游开发 Role Harness v2(Godot)

一套"每个 Claude session 扮演一个固定 role"的协作规范,面向 **solo 独游开发**。
是旧体系(`../Claude_Roles/` + `../Skills/`)的继任者:骨架按**游戏生命周期四期**重组,
**GameDesignDocs 理论库**通过双层机制注入每个 role,并补齐立项/试玩/bug/发布四个环节。
旧体系继续服务存量项目,新项目一律用本套。设计决策全录见 `DESIGN.md`。

## 核心思想

1. **一 session 一 role,靠 artifact 协作**:session 之间不共享对话上下文,协作全靠
   `harness/` 下的产物文件。换棒 = `/clear` + 下一条 `/g-*` 命令。
2. **理论有源,实践有形**:每个 role 内嵌从 GameDesignDocs 蒸馏的判断框架(标注出处
   与日期),争议时按需下钻原文;理论库是权威源,role spec 是编译产物。
3. **全 harness 一套记号**:`[ ]` 未开始 · `[~]` 进行中 · `[x]` 完成或确认不需要。
   PLAN 步骤、REVIEW must-fix、BUG/RELEASE checklist、HANDOFF 状态行,全部同一套。

## 四期骨架(17 个 role × 五种工作模式)

```
┌─ 立项期(每游戏一次)──────────────────────────────────────┐
│  /g-kickoff     点子 → 目标玩家/核心体验/设计支柱            │
│                 → v1 范围(含最小系统集)→ GAME-BRIEF.md      │
├─ 功能期(逐 feature 循环)─────────────────────────────────┤
│  /g-design-jam  功能点子前门 → IDEA.md                       │
│  /g-designer → /g-planner → /g-implementer → /g-reviewer     │
│  (/g-explorer 进陌生代码时;/g-art-spec、/g-integrator 按需)  │
├─ 调优期(回路,随时进出)─────────────────────────────────┤
│  /g-playtest    试玩脚本 ↔ 人玩回报闭环(也是功能收尾 gate)   │
│  /g-bugfix      轻量 bug 通道:复现→根因→修→回归              │
├─ 发布期(每版本一次)──────────────────────────────────────┤
│  /g-release     导出/回归/版本号/上架 ↔ 人执行回报闭环        │
└─ 横跨全程 ────────────────────────────────────────────────┘
   /g-producer                      范围守门 + INBOX 分诊 + 归档
   /g-arch-guard /g-num-smith       四事实源守护
   /g-state-machine /g-ux-design
```

| 工作模式 | 成员 | 特征 |
|---|---|---|
| 常驻型 | producer | 横跨全程,守 BACKLOG/INBOX,独家分诊 |
| 仪式型 | kickoff、release | 每游戏/每版本一次 |
| 管线型 | design-jam、designer、explorer、planner、implementer、reviewer、art-spec、integrator | 占 HANDOFF 管线行,逐 feature 接力 |
| 回路型 | playtest、bugfix | 三态记号驱动闭环,随时进出 |
| 守护型 | arch-guard、num-smith、state-machine、ux-design | 各守一份事实源,被 escalation 路由唤起 |

两类交付性质务必分清:**自交付**(designer/planner/implementer/reviewer/bugfix 等:
写出文档/代码 = 干完活)与**规格+验收**(art-spec、integrator、playtest、release:
Claude 画不了图、进不了编辑器、玩不了游戏,它们产出规格/步骤/判据,**你执行后回报,
它再验收**——这层回报闭环是它们能用的前提)。

## 功能期管线(每个 feature 走的路)

```
(IDEA.md ←/g-design-jam,可选)
   ↓
/g-designer ── FEATURE-DESIGN.md ──→ /g-planner ── PLAN.md ──→ /g-implementer
   (/g-explorer ── CONTEXT-FINDINGS.md ──↗ 进陌生代码时)          │
                                              CHANGES.md(含 Wiring Contract)
                                                   ↓
                     /g-reviewer ── REVIEW.md(实现↔审查回环)
                                                   ↓
        /g-art-spec ── ASSET-SPEC.md → 你生图 → ACCEPTANCE.md(美术↔人回环,可选)
                                                   ↓
        /g-integrator ── INTEGRATION-STEPS.md(接线↔人回环,可选)
                                                   ↓
        /g-playtest ── PLAYTEST.md(试玩↔人回环,**默认必经收尾 gate**)
                                                   ↓
                        功能完成 → /g-producer 归档
```

关键接缝:implementer 的 CHANGES.md 里有一节 **Wiring Contract**(新脚本暴露哪些
`@export`、发什么 signal、要哪些 autoload/input action/group)——integrator 把它
翻译成编辑器接线清单,没这节它只能猜。四个验收回环的闭合规则写死在
`templates/HANDOFF.md`,全项目严格照走。

## Artifact 一览(session 之间真正的"共享状态")

| 文件 | 产出者 | 位置 |
|------|--------|------|
| GAME-BRIEF.md | kickoff | `harness/`(常驻,支柱与范围的唯一权威) |
| project-context.md | kickoff 起草,你维护 | `harness/`(常驻,施工事实) |
| BACKLOG.md | producer | `harness/`(常驻) |
| INBOX.md | 谁都可追加,producer 分诊 | `harness/`(常驻) |
| STYLE-BIBLE.md | art-spec | `harness/`(常驻) |
| ARCHITECTURE / BALANCE / STATE-MACHINES / UX-MAP.md | 四守护 role | `harness/`(常驻事实源) |
| IDEA / FEATURE-DESIGN / CONTEXT-FINDINGS / PLAN / CHANGES / REVIEW / ASSET-SPEC / ACCEPTANCE / INTEGRATION-STEPS / PLAYTEST / HANDOFF | 管线型 + playtest | `features/<NN-slug>/` |
| BUG.md | bugfix | `bugs/<NN-slug>/` |
| RELEASE.md | release | `releases/<version>/` |
| TUNE-<NN>.md | playtest(整体调优) | `playtest/` |
| ARCH/BALANCE/STATE/UX-CHANGE-<NN>.md | 四守护 role | `{arch,balance,state,ux}/` |

frontmatter 约定与全量枚举见 `templates/artifact-frontmatter.md`。

## 理论库对接(GameDesignDocs)

- **双层机制**:role spec 内嵌蒸馏后的检查清单/判断框架(标 `蒸馏自 ... @ 日期`),
  日常零额外读取;**只在三种情形下钻原文**——推翻上游前、记录原则冲突时(按
  conflict-resolution.md 流程)、你明确要求时。理论库根目录填在 project-context §0,
  留空则下钻自动跳过。
- **导航用库自带的地图**:principle-map(按问题)、system-map(按系统),role 不自建索引。
- **回流**:实践中发现理论有错/缺,role 记 `理论疑点:` 进 HANDOFF flags,由你带回理论库。
- **三层对应**:Philosophy → kickoff/designer 的判断力;Systems → 四事实源(理论在项目内
  的实例化);Specifications → per-feature artifact。

**流程话语对照**(GameDesignDocs `process/` 是规范层,本 harness 是其 AI 实现层;
功能状态不设显式字段,由现有记号推导):

| process/ 规范状态 | harness 实况 |
|---|---|
| Inbox | INBOX.md 一行 / BACKLOG 未分诊 |
| Clarifying | IDEA / FEATURE-DESIGN 处于 draft |
| Ready | FEATURE-DESIGN accepted + 进 BACKLOG Now |
| In Progress / In Review / Testing | HANDOFF 实现 `[~]` / 审查 `[~]` / 试玩 `[~]` |
| Done | 功能完成判据满足,归档 |
| Released | 出现在某 releases/<version>/ 清单 |
| Blocked / Deferred / Rejected | HANDOFF flags / BACKLOG Later / BACKLOG Cut |

## 用 /g-* 命令激活

```
/g-kickoff                          # 立项(新游戏第一步,见 BOOTSTRAP.md)
/g-designer 01-double-jump          # 管线型 role 带功能 slug
/g-implementer 01-double-jump auto  # slug 后可跟自由指令;implementer 支持 auto 模式
/g-bugfix "跳跃后偶尔卡进墙里"       # bug 通道:给现象描述或已有 bug slug
/g-release v0.2                     # 发布仪式带版本号
/g-producer                         # 常驻 role 可不带参数
```

规则:管线型 role 应带 slug;不带 slug 且恰好只有一个 `in-progress` 功能时自动选它,
否则停下来问;slug 命名 `<两位序号>-<kebab>`。**换棒必用 `/clear`**(不是 /compact——
compact 残留上一个角色的上下文,正好犯"契约打架/fresh eyes 失效"的毛病)。每个 role
干完都会打印交棒行:`已完成 — 下一步:/g-xxx <slug>(切换前先 /clear)`,照抄即可。

**开场先握手**:激活后 role 先用中文 1-3 行汇报"这是哪个工作单元、本 session 打算干什么",
问一句"有什么要先补充或调整的吗?",等你确认才动手。纯查状态的提问直接回答。

## 防上下文噪音

- **状态在前、历史在后/外**:HANDOFF 的管线状态+下一步、BACKLOG 的 Now/Next、GAME-BRIEF、
  四事实源当前形态是必读状态;决策记录/Decision log/CHANGE 文档/BUG/RELEASE 是账本,按需读。
- **事实源只写当前真相**,变更史归 `{arch,balance,state,ux}/*-CHANGE-NN.md`。
- **功能 done 后归档**到 `harness/archive/`,出默认读取范围;bugs/releases/playtest 目录
  本身就是账本,role 默认不扫。细则见 `templates/artifact-frontmatter.md`「状态 vs 历史」。

## 中途捕获(INBOX)

开发途中冒出的新想法:当前 role 追加一行到 `harness/INBOX.md`、回显、继续手头活——
不动要求文件、不中断当前功能。只有 producer 分诊。例外:新输入说明当前任务已走错 →
role 按各自 escalation 停下上报。细则见 `templates/INBOX.md`。

## 起手顺序(别一上来全铺开)

1. 新游戏:照 `BOOTSTRAP.md` 走——建工程 → 复制骨架 → `/g-kickoff` 立项 →
   四守护开局 → `/g-producer` 初版 BACKLOG。
2. 第一个功能把整条链走通:designer → planner → implementer → reviewer → playtest
   (art-spec/integrator 视功能需要)。
3. 链顺了再常态化:producer 定期分诊 INBOX;bug 走 `/g-bugfix`;攒够一批 Shipped 就
   `/g-release`。

## 文件清单

- `README.md` ........................ 本文件
- `DESIGN.md` ........................ 设计决策全录(D1~D9、审计缺陷修法、蒸馏映射表)
- `BOOTSTRAP.md` ..................... 新游戏铺地基 checklist
- `role-template.md` ................. 17 role 共用母版(造新 role 时复制)
- `skills/g-<name>/SKILL.md` ......... 17 个 role,一 role 一目录,**源即安装形态**
  (常驻:producer;仪式:kickoff、release;管线:design-jam、designer、explorer、
  planner、implementer、reviewer、art-spec、integrator;回路:playtest、bugfix;
  守护:arch-guard、num-smith、state-machine、ux-design——工作模式看各文件
  `<work_mode>` 段与上方模式表)
- `templates/` ....................... 复制进游戏项目的骨架(project-context、GAME-BRIEF、HANDOFF、INBOX、BUG、RELEASE、artifact-frontmatter、style-basic-2d)

安装:`skills/` 已是安装形态(每个 role 自带 SKILL.md frontmatter),整目录拷进
`~/.claude/skills/` 即可用:

```
cp -r skills/g-* ~/.claude/skills/     # Windows 资源管理器:把 skills/ 下的 g-* 文件夹全选拷过去
```

本仓库为源、安装为副本,改源后重拷,并核对:
`for d in skills/g-*; do diff -q "$d/SKILL.md" ~/.claude/skills/"${d#skills/}"/SKILL.md; done`
(宿主载体是 Claude Code 的 skill 格式,那只是安装机制,不是体系内分类)。
