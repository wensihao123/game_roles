# Game_Roles 设计文档

> 状态:待审  
> 日期:2026-07-11(同日第三版:v2 对齐完工后的理论库;v3 撤销 role/skill 二分,统一为 role × 五种工作模式)  
> 目的:定义一套全新的、完整自洽的游戏开发 Role Harness,取代旧体系(Claude_Roles + Skills)用于**新**游戏项目,并首次把 GameDesignDocs 理论库系统性地接入 AI 辅助开发流程。  
> 本文档是后续逐文件实施的唯一蓝图。

---

## 1. 背景与动机

- 旧体系(`../Claude_Roles/` 8 role + `../Skills/` 6 skill)有项目在用,不能原地修改。
- 2026-07-11 的全量审计发现旧体系存在 23 项缺陷,分三档:错漏(悬空命令、自相矛盾的交棒、枚举漏项)、一致性缺口(UX gate 不对称、事实源重叠无 owner)、管线覆盖缺口(无立项/试玩/bug 通道/发布)。
- GameDesignDocs(`G:/Codes/GameDesignDocs`,理论库:design 理念 / engineering 工程 / process 流程)与旧体系**零连接**——role 各自硬编码"平行小抄",与理论文档同源不同文,持续漂移。
- 结论:新建 Game_Roles,参考旧体系被验证过的机制内核,按新骨架重设计,理论作为体系的组织原则而非补丁。

## 2. 核心决策记录

| # | 决策 | 备选与理由 |
|---|------|-----------|
| D1 | Game_Roles 是**完整自洽的新一套**(roles + 游戏向 skills),不依赖旧仓库 | 备选"只重设计 roles 复用旧 skills"会把审计缺陷带进新体系且新旧耦合 |
| D2 | 命令用 **`/g-` 前缀**新命名空间 | 旧 `/role-*` 装在 `~/.claude/` 全局生效,同名必撞车;短前缀因为高频敲击 |
| D3 | 理论注入用**双层机制**:蒸馏内嵌为主 + 按需下钻 | 直接读理论文档烧上下文且路径绑死本机;纯内嵌则理论更新后静默漂移 |
| D4 | 骨架按**游戏生命周期四期**组织(立项→功能→调优→发布) | 与 GameDesignDocs 三板块结构同构:philosophy→立项/设计,engineering→功能期,process→生命周期本身 |
| D5 | **不等 GameDesignDocs 完工**,先设计 roles | 已完工部分恰好覆盖最需要的理论;role 设计反向拉动剩余理论写作;双层机制天生支持异步演化 |
| D6 | **去掉 image-prompt**,美术链路止于 ASSET-SPEC | 用户已有外部生图工具;风格一致性职责收归 STYLE-BIBLE |
| D7 | v1 新增四环节:**游戏级立项、playtest/手感、bug 轻量通道、发布/导出** | 审计确认的管线两头空白 |
| D8 | GameDesignDocs 的 `process/` 是**规范层**,Game_Roles 是其 AI 实现层 | 消除两套流程话语并行的"该信哪份"问题 |
| D9 | **撤销 role/skill 二分,统一称 role**,按五种工作模式分类(常驻/仪式/管线/回路/守护) | 二分是旧体系两仓库分家的遗留:新体系中两者安装方式、调用方式、行为契约完全同构,分类判据与实例对不上(kickoff 交互最重却叫 skill,bugfix 常一次跑完却叫 role);工作模式才是有信息量的轴。role 一词保留因为拟人格(`<role_identity>`)是行为契约的核心机制,且"一 session 一 role"是已内化的第一纪律 |

## 3. 总体架构

### 3.1 四期骨架与成员

全部 **17 个 role** 统一契约:同一份母版模版、同一安装方式、`/g-*` 激活、一 session 一 role。按**五种工作模式**分类(见骨架图后的模式表)。

```
┌─ 立项期(每游戏一次)──────────────────────────────────────┐
│  /g-kickoff     点子 → 目标玩家/核心体验/设计支柱            │
│                 → v1 范围(含最小系统集)→ GAME-BRIEF.md      │
│                 → 引导四事实源开局 + Producer 初版            │
├─ 功能期(逐 feature 循环)─────────────────────────────────┤
│  /g-design-jam  功能点子前门 → IDEA.md                       │
│  /g-designer  /g-explorer  /g-planner  /g-implementer        │
│  /g-reviewer  /g-art-spec  /g-integrator                     │
├─ 调优期(回路,随时进出)─────────────────────────────────┤
│  /g-playtest    试玩脚本+手感参数方案 ↔ 人玩回报闭环          │
│  /g-bugfix      轻量 bug 通道:复现→根因→修→回归              │
├─ 发布期(每版本一次)──────────────────────────────────────┐
│  /g-release     导出/回归/版本号/上架 ↔ 人执行回报闭环        │
└─ 横跨全程 ────────────────────────────────────────────────┘
   /g-producer                      范围守门 + INBOX 分诊 + 归档
   /g-arch-guard /g-num-smith       四事实源守护
   /g-state-machine /g-ux-design
```

| 工作模式 | 成员 | 特征 |
|---|---|---|
| 常驻型 | producer | 横跨全程,守 BACKLOG/INBOX,独家分诊 |
| 仪式型 | kickoff、release | 每游戏/每版本一次,产出常驻或版本 artifact |
| 管线型 | design-jam、designer、explorer、planner、implementer、reviewer、art-spec、integrator | 占 HANDOFF 管线行,逐 feature 接力 |
| 回路型 | playtest、bugfix | 三态记号驱动闭环,随时进出(playtest 的功能试玩模式同时占 HANDOFF 试玩行) |
| 守护型 | arch-guard、num-smith、state-machine、ux-design | 各守一份事实源,被 escalation 路由唤起 |

任何单个功能只经过其中一小段;分期的意义是永远只需知道"现在在哪一期,这期有谁"。

### 3.2 本仓库布局

```
Game_Roles/
  README.md                  # 总览:四期骨架图 + 怎么用 + 流程话语对照表(见 5.1)
  DESIGN.md                  # 本文件(实施完成后转为决策存档)
  BOOTSTRAP.md               # 铺地基 checklist,第一步就是 /g-kickoff
  role-template.md           # 造新 role 的母版
  roles/
    standing/  producer.md
    ceremony/  kickoff.md release.md
    pipeline/  design-jam.md designer.md explorer.md planner.md
               implementer.md reviewer.md art-spec.md integrator.md
    loop/      playtest.md bugfix.md
    guardian/  arch-guard.md num-smith.md state-machine.md ux-design.md
  templates/   project-context.md GAME-BRIEF.md HANDOFF.md INBOX.md
               BUG.md RELEASE.md artifact-frontmatter.md style-basic-2d.md
```

### 3.3 游戏项目侧 harness/ 布局

在旧体系基础上新增三处(标 ★):

```
<game-project>/harness/
  GAME-BRIEF.md              ★ 立项产物,常驻,与 project-context 并列人人必读
  project-context.md           (新增"理论库:"字段,见 4.2)
  BACKLOG.md  INBOX.md  STYLE-BIBLE.md  style-basic-2d.md
  ARCHITECTURE.md  BALANCE.md  STATE-MACHINES.md  UX-MAP.md
  arch/ balance/ state/ ux/     (各类 <KIND>-CHANGE-<NN>.md)
  features/<NN-slug>/           (per-feature artifact + HANDOFF.md)
  bugs/<NN-slug>/             ★ BUG.md 单文件轻量通道
  releases/<version>/         ★ RELEASE.md 每版本一份
  archive/                      (done 后整目录归档)
```

### 3.4 继承不变的机制内核

以下机制旧体系已验证,原样保留:artifact 协作 + frontmatter、每功能 HANDOFF、三态记号 `[ ]/[~]/[x]`(阻塞不是第四种状态)、验收回环闭合原则(验收方 `[x]` 当且仅当无 open 待办)、INBOX 中途捕获·Producer 独家分诊、事实源"只写当前形态,变更史归 CHANGE 文档"、done 后归档出默认读取范围、`/clear` 换棒·一 session 一 role、开场握手、结尾交棒行、规格+验收型 role 的人机回报闭环、Implementer 的 Manual/Auto 双模式与 Wiring Contract、Planner 一次裁定可选阶段。

## 4. 理论注入:双层机制

**三层对齐**(理论库完工后新增的总纲):GameDesignDocs 自述其设计文档分三层——Philosophy(为什么)→ Systems(产品如何运转)→ Specifications(具体功能)。Game_Roles 与之逐层对应:

| GameDesignDocs 层 | harness 对应物 |
|---|---|
| Philosophy(foundation/experience/long-term/responsibility/validation + 两张地图) | g-kickoff 与 g-designer 的判断框架;GAME-BRIEF 的支柱与取舍 |
| Systems(7 系统群 24 篇 + system-map/framework/integration-rules) | **四事实源(ARCHITECTURE/BALANCE/STATE-MACHINES/UX-MAP)= Systems 理论在项目内的实例化**;各守护型 role 是对应系统群的执行者 |
| Specifications | FEATURE-DESIGN / PLAN / ASSET-SPEC 等 per-feature artifact |

### 4.1 第一层:蒸馏内嵌

嵌入 role spec 的不是理论原文,而是**可执行形态**(检查清单、判断七问、决策路由表)。每段蒸馏内容标注 `蒸馏自 GameDesignDocs/<path> @ <YYYY-MM>`,理论大改后 grep 日期戳即知哪些 spec 要重蒸馏。

理论库已全部完工,下表为全量对齐版(路径以 philosophy 子目录重组后为准):

| role | 内嵌蒸馏物 | 理论来源 |
|---|---|---|
| g-kickoff | 核心体验框架(Fantasy/Emotional Goal/Core Loop/支柱);目标玩家画像问题组;默认优先级(正确安全→信任伦理→可理解→可控→可恢复→核心体验→长期深度→效率…)与冲突处理流程;证据等级表;**v1 最小系统集裁剪**(核心循环优先、支持裁剪) | philosophy/foundation/ 三篇、conflict-resolution、systems/system-map |
| g-designer | "如何评审一个设计"七问自检;每份设计必答"支持哪些支柱/牺牲哪些/是否值得";**系统域路由**(功能落在哪个系统群→下钻对应系统篇)+ 跨系统接缝检查;体验细则按维度下钻 experience/ 五篇(清晰反馈/简deep/选择后果/挑战公平/节奏) | foundation/design-principles、principle-map、systems/system-map、integration-rules;experience/ 五篇为下钻层 |
| g-planner | P1~P6 决策路由表;模块边界判据;合格任务七条标准;DoR 就绪门;**跨系统改动检查**(接缝处的状态所有权/依赖方向) | patterns、module-boundaries、task-breakdown、definition-of-ready、systems/integration-rules |
| g-implementer | 七类避坑症状索引(动什么代码→先查哪篇) | pitfalls |
| g-reviewer | 统一评审轴 = 旧 5 轴(正确/忠实/安全/过度工程/约定)∪ review-checklist(隐藏依赖/重复逻辑/硬编码/存档影响/文档/命名);DoD 完成门 | review-checklist、definition-of-done |
| g-producer | 里程碑=垂直切片判据;DoR 作进 Now 门槛;分诊规则 | roadmap、backlog、DoR |
| g-playtest | 十二维测试清单→试玩脚本;假设驱动试玩(假设/失败标准/防自欺,正式蒸馏自已完工的验证篇);难度与挑战的试玩维度 | testing-checklist、philosophy/validation/iteration-and-validation、systems/progression/difficulty-and-challenge |
| g-bugfix | 九步 bug 流(复现→严重度→影响面→根因→修→回归→相邻系统→补测试?→更新文档?) | bugfix-workflow |
| g-release | 十步发布流(冻结→回归→存档兼容→导出测试→版本号→changelog→构建→发布→验证→回滚);**版本与迁移判据**(存档兼容检查的理论源) | release-workflow、systems/operations/versioning-and-migration |
| g-art-spec / g-integrator | AI 资产纪律(风格/角色一致性、透明背景、贴纸白边、spritesheet/pivot、导入参数) | ai-asset-workflow、asset-workflow |
| g-arch-guard | SOLID-in-Godot 判据摘要;**状态所有权唯一/依赖方向/故障边界**三原则;存档与持久化归属(ARCHITECTURE 是存档格式唯一 owner 的理论依据) | solid-in-godot、systems/system-map、systems/player/save-and-persistence |
| g-num-smith | (自带数值框架保留)+ **资源流闭合六要素**(来源/用途/消耗/回收/上限/所有权);奖励结构与难度曲线理论 | systems/progression/resources-and-economy、reward-system、difficulty-and-challenge |
| g-state-machine | 十类 FSM 分类坐标系(按类下钻);游戏状态与流程的系统理论 | fsm-index、systems/core/game-state-and-flow |
| g-ux-design | 交互状态(游戏化词汇);输入与交互约定;设置/引导/通知三系统的交互理论 | systems/core/input-and-interaction、systems/player/(settings / tutorial-and-onboarding / notification-and-reminders) |

**刻意不内嵌**:systems/social/(匹配/审核/多人)、systems/commercial/(货币化/定价/权益)、systems/operations/ 的 live-ops/experiment/analytics 三篇——与 solo 独游 v1 基本无关,项目真涉及时按需下钻,不预占任何 role 的上下文。philosophy/responsibility/ 两篇(可访问性/伦理)蒸馏为 g-designer 与 g-reviewer 各一条检查项(不整篇内嵌),原文作下钻层。

### 4.2 第二层:按需下钻

- **路径解耦**:`project-context.md` 新增字段 `理论库: <GameDesignDocs 根目录>`(每项目填一次)。spec 内只写相对路径 `<理论库>/engineering/patterns/p1-communication.md`。字段留空时下钻降级为跳过,spec 保持可移植。
- **下钻只在三种情形**(写死在 spec,防滥用烧上下文):
  1. **推翻上游前**——escalation 之前先下钻对应理论,核实异议站得住;
  2. **记录原则冲突时**——按 `philosophy/conflict-resolution.md` 的流程与记录格式(冲突/情境/选择/理由/后果/复审条件)写进 artifact 之前,读原文;
  3. **人明确要求时**。
  日常工作只用内嵌蒸馏层,零额外读取。
- **下钻导航不自建**:理论库已自带三份导航件——`principle-map.md`(按问题路由理念)、`system-map.md`(按系统域路由)、`glossary.md`(术语)。role spec 内嵌的路由表一律**蒸馏自这两张地图并注明"地图为准"**,不另行发明索引——否则就是在新体系里复刻"平行小抄漂移"的老病。
- **理论回流**:role 发现理论有错/有缺/蒸馏脱节,不得自改理论库;在 HANDOFF flags 记 `理论疑点: <篇目> <一句话>`,由人带回 GameDesignDocs 处置。

## 5. 机制内核修缮

### 5.1 流程话语统一(GameDesignDocs process/ = 规范层)

README 放对照表;功能状态**不设显式字段,全部由现有记号推导**:

| process/ 规范状态 | harness 实况 |
|---|---|
| Inbox | INBOX.md 一行 / BACKLOG 未分诊 |
| Clarifying | IDEA / FEATURE-DESIGN 处于 draft |
| Ready | FEATURE-DESIGN accepted + 进 BACKLOG Now(DoR 门由 producer/planner 把守) |
| In Progress | HANDOFF 实现 `[~]` |
| In Review | HANDOFF 审查 `[~]` |
| Testing | HANDOFF 试玩 `[~]` |
| Done | 功能完成判据满足,归档 |
| Released | 出现在某 releases/<version>/ 清单 |
| Blocked / Deferred / Rejected | HANDOFF flags / BACKLOG Later / BACKLOG Cut |

### 5.2 HANDOFF v2

管线表**一行一 artifact**,可选行由 planner 一次裁定(`[x] 不需要`):

```
| 阶段          | artifact            | 状态 |
| 点子(可选)    | IDEA.md             |      |
| 设计          | FEATURE-DESIGN.md   |      |
| 勘探(可选)    | CONTEXT-FINDINGS.md |      |
| 计划          | PLAN.md             |      |
| 实现          | CHANGES.md          |      |
| 审查          | REVIEW.md           |      |
| 美术规格(可选) | ASSET-SPEC.md       |      |
| 资源验收(可选) | ACCEPTANCE.md       |      |
| 接线(可选)    | INTEGRATION-STEPS.md|      |
| 试玩          | PLAYTEST.md         |      |
```

- **试玩是默认必经的收尾 gate**(接线后、done 前)——同时修掉旧审计 #21"集成引入的问题无人复核"。
- bugfix / release **不进功能 HANDOFF**:BUG.md、RELEASE.md 单文件自记 checklist。
- 交棒行统一短格式:`已完成 — 下一步:/g-xxx <slug>(切换前先 /clear)`(HANDOFF 模版与各 role signoff 一字不差)。
- 功能完成判据:所有非"不需要"行 `[x]` 且审查 verdict 非 REQUEST CHANGES 且试玩 `[x]` → status 翻 done,交 Producer 归档。

### 5.3 artifact 分类学补全

- `artifact:` 枚举一次列全:IDEA / GAME-BRIEF / FEATURE-DESIGN / CONTEXT-FINDINGS / PLAN / CHANGES / REVIEW / ASSET-SPEC / ACCEPTANCE / INTEGRATION-STEPS / PLAYTEST / BUG / RELEASE / ARCH-CHANGE / BALANCE-CHANGE / STATE-CHANGE / UX-CHANGE。
- arch-guard 产物**改名 `ARCH-CHANGE-<NN>`**(弃 REFACTOR,归队 `<KIND>-CHANGE` 模式)。
- HANDOFF 三字段 frontmatter(feature/status/updated)作为特例写进 artifact-frontmatter.md;GAME-BRIEF 按常驻文件规则只留 `updated:`;BUG / RELEASE / TUNE 的 `feature:` 字段分别填 bug slug / 版本号 / tune 编号(该字段语义放宽为"所属工作单元")。
- 所有 role 的常驻文件枚举**统一同一份完整清单**:project-context、GAME-BRIEF、BACKLOG、INBOX、STYLE-BIBLE、style-basic-2d、ARCHITECTURE、BALANCE、STATE-MACHINES、UX-MAP。
- INBOX 捕获行统一 `- [<YYYY-MM-DD>][来自 <feature>/<role> 或 用户][优先级或?] <想法>`。

### 5.4 对称性修复

- **四事实源全对称**:planner 必读四份、冲突各自路由(补 UX-MAP,修 gate 不对称);explorer 四份列为可选输入;implementer 的 escalation 补全 `/g-arch-guard` `/g-num-smith` `/g-state-machine` `/g-ux-design` 具体路由(修 Auto 模式引用落空);四守护 role 输入对称(arch-guard 补读 FEATURE-DESIGN)。
- **重叠定 owner**:屏幕间**转移表归 STATE-MACHINES.md**,UX-MAP §2 只持有屏幕清单+每屏职责+交互状态,引用不复制;**存档/序列化格式归 ARCHITECTURE.md** 单一持有,num-smith / state-machine 谈存档迁移时引用它。
- **ux-design 交互状态词汇游戏化**:default/focus/pause/transition/cutscene/gameover 等,替换 Web 味的 loading/empty/error(仅保留对游戏真实存在者)。
- wikilink 等格式约定四守护 role 统一(实施时定一种,全体遵守)。

### 5.5 美术链路(image-prompt 移除后)

g-art-spec 出 ASSET-SPEC.md(资源清单+规格:尺寸/透明背景/pivot/风格约束,直接可喂外部生图工具)→ 人生图 → g-art-spec 验收出 ACCEPTANCE.md(fail 项三态记号回环)→ g-integrator 接线。风格一致性由 STYLE-BIBLE.md 承担:ASSET-SPEC 每份规格直接引用风格圣经的约束段落,保证跨资源统一。

## 6. 新环节详设

### 6.1 /g-kickoff(仪式型 role,每游戏一次)

- **触发**:BOOTSTRAP 第一步;输入 = 你的游戏点子(一句话到一页纸)。
- **工作流**(对话式,一次一问):
  1. 点子捕捉:原样复述,确认抓对;
  2. 核心体验收敛:玩家幻想 → 情绪目标 → 核心循环(动作→结果→反馈→再来一次的动机);
  3. 目标玩家:给谁玩、他们现在玩什么、凭什么值得他们的时间;
  4. 设计支柱提炼:2~4 条,**每条必须带取舍面**(接受牺牲什么),防口号化;
  5. v1 范围切割:垂直切片式"可上架最小版本"+ 明确不做什么 + **v1 最小系统集**(对照 system-map 的七系统群逐群裁定:v1 必须有 / 明确砍掉——砍掉的记入"不做什么"防止回潮);
  6. 风险清单:最可能杀死项目的 2~3 个假设,各配最便宜的验证方式。
- **产出**:`harness/GAME-BRIEF.md`(六项,§5 含最小系统集)+ 代填第 0 节的 project-context.md 草稿。
- **收尾**:打印后续仪式清单——四事实源开局(各跑一次模式 A,**读 GAME-BRIEF 并按其最小系统集初始化**,砍掉的系统群不建条目)→ `/g-producer` 建初版 BACKLOG。立项期完成判据:GAME-BRIEF + project-context + 四事实源 + BACKLOG 五件套齐活。

### 6.2 /g-playtest(回路型 role,规格+验收)

- **两种模式**:功能试玩(带 slug,功能收尾 gate,产出该功能目录下的 PLAYTEST.md)/ 整体调优(不带 slug,跨功能手感巡检,产出 `harness/playtest/TUNE-<NN>-<topic>.md`,与 CHANGE 系列同款自编号)。
- **工作流**:读 FEATURE-DESIGN 成功判据 + GAME-BRIEF 支柱 → 生成试玩脚本(假设清单 + 十二维裁剪的操作步骤 + 每步"观察什么/什么算失败")→ 人玩回报 → 逐假设判定 pass/fail → fail 翻译成可调参数建议(手感:coyote time/输入缓冲/tween 曲线/镜头阻尼;数值转 `/g-num-smith`;设计根因转 `/g-designer`)→ 人调完再回报,环闭合。
- **产出**:PLAYTEST.md(假设清单三态记号驱动,与美术验收回环同构)。
- **防自欺条款**:脚本必须含至少一条证伪型问题("如果玩家做 X 说明设计失败"),不许全是"确认好不好玩"。

### 6.3 /g-bugfix(回路型 role,自交付·轻量通道)

- **触发**:`/g-bugfix "<现象描述>"` 或 `/g-bugfix <NN-slug>`(续做)。
- **工作流**:九步流内嵌为 BUG.md checklist——复现(拿不到复现 STOP 要)→ 严重度/影响面 → 根因(证据先于修复,禁止猜改)→ 最小修复 → 回归验证 → 相邻系统检查 → 裁定补测试/更新文档与事实源 → 收尾。
- **产出**:`harness/bugs/<NN-slug>/BUG.md` 单文件自记全程;不开 HANDOFF、不走 review。
- **两个升级出口**:根因牵扯架构/数值/状态机 → 转对应守护型 role;修复超出"最小修复"(多文件/改接口)→ 升级为正式 feature,Producer 记账。
- **护栏**:修复必须先有失败复现、后有通过验证,均记录在 BUG.md。

### 6.4 /g-release(仪式型 role,规格+验收,每版本一次)

- **触发**:`/g-release <version>`;输入 = BACKLOG Shipped 区 + 上一版 RELEASE.md。
- **工作流**:十步流生成发布清单(功能冻结确认 → 回归范围裁定[读 bugs/ 历史挑高危区] → 存档兼容 → 导出测试[headless 命令 + 人跑真机导出包回报] → 版本号/changelog → 构建 → 上架步骤 → 发布后验证 → 回滚预案)→ 人逐项执行回报,三态记号驱动,全 `[x]` 才判 Released。
- **产出**:`harness/releases/<version>/RELEASE.md`;附带义务:BACKLOG Shipped 区标记版本号,通知 Producer 归档。

## 7. 实施顺序

按依赖序分五批,每批完成即可独立验收:

1. **地基**:role-template.md → templates/(artifact-frontmatter / HANDOFF / INBOX / project-context / GAME-BRIEF / BUG / RELEASE / style-basic-2d 移植)→ README.md 骨架 + 话语对照表 → BOOTSTRAP.md;
2. **管线型 role**(从旧体系蒸馏移植 + 修缮 + 理论内嵌):design-jam / designer / explorer / planner / implementer / reviewer / art-spec / integrator;
3. **守护型 + 仪式型(上)**:四守护 role 移植 + 5.4 修缮;kickoff 新写;
4. **回路型 + 仪式型(下)+ 常驻型**:playtest / bugfix / release 新写;producer 移植修缮;
5. **命令接线**:每个 role 一个 `~/.claude/skills/g-<name>/SKILL.md`(宿主载体仍是 Claude Code 的 skill 格式——那只是安装机制,不是体系内分类;本仓库为源,安装为副本,改源后重拷 + `diff -q` 核对);端到端验证。

**验证方式**:建一个玩具 Godot 项目,完整走一遍 `/g-kickoff → g-design-jam → designer → planner → implementer → reviewer → art-spec → integrator → playtest → release`,外加一次 `/g-bugfix`,确认每个交棒行可照抄执行、每个 artifact 冷启动可读。

## 8. 理论对齐状态

2026-07-11 复核:philosophy 层(13 篇 + conflict-resolution/principle-map/glossary)与 systems 层(7 系统群 24 篇 + 4 份框架件)已全部完工,§4.1 所有来源就绪,**无待补标记**。原"理论待补"机制保留在 spec 规范里,供理论库未来新增篇目时使用。

仍空白但不阻塞:

- `design/case-studies/`——将来有拆解案例后,作为 g-designer / g-kickoff 的下钻素材(参考"哪些内容不能直接复制"的判断);
- `inbox/`——理论库自己的收集箱,与 harness 无涉。

实施时的蒸馏统一以**当日理论库 HEAD** 为基线打日期戳。

## 9. 明确不做(v1)

- 音频独立环节:暂由 g-art-spec 名义代管,量大后再拆(旧体系同策略);音频规格纪律文档(style-audio)v1 不写。
- 叙事/本地化/性能专项:无对应 role,记入已知未覆盖。
- 旧体系与 GameDesignDocs 的直接衔接:旧体系冻结不动,只服务存量项目。
- Plugin marketplace 分发:先本机 `~/.claude/` 安装,分发后议。
