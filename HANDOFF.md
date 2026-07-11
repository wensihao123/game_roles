---
feature: game-roles-implementation
status: in-progress
updated: 2026-07-11
---
# HANDOFF — Game_Roles 体系实施

> 本文件是 Game_Roles 仓库**自身建设**的交接状态(吃自己的狗粮)。
> 冷启动读法:先读本文件「管线状态 + 下一步」,再读 `DESIGN.md`(实施蓝图,含 D1~D9
> 决策、§4.1 蒸馏映射表、§5 修缮清单、§7 分批顺序)。历史讨论无需追溯,DESIGN.md 即全部共识。

## 管线状态(对应 DESIGN §7 五批)

| 批次 | 内容 | 状态 |
|------|------|------|
| 1 地基 | role-template / templates×8 / README / BOOTSTRAP | [x] commits 9829c6b + 7acdefe |
| 2 管线型 role | design-jam / designer / explorer / planner / implementer / reviewer / art-spec / integrator | [x] commits 62f2759…305842f |
| 3 守护型 + kickoff | arch-guard / num-smith / state-machine / ux-design 移植修缮;kickoff 新写 | [x] commits 9ded5a4…(见 log) |
| 4 回路 + 仪式(下)+ 常驻 | playtest / bugfix / release 新写;producer 移植修缮 | [ ] |
| 5 命令接线 | 拷入 `~/.claude/skills/g-<name>/`;玩具项目端到端验证 | [ ] |

## 下一步

**批 4:回路型 + 仪式型(下)+ 常驻型。** 逐个流程:

1. **playtest 新写**(`roles/loop/playtest.md`):无底稿,按 DESIGN §6.2 详设写。
   两种模式:功能试玩(带 slug,占 HANDOFF 试玩行,产出 PLAYTEST.md)/ 整体调优
   (不带 slug,产出 `harness/playtest/TUNE-<NN>-<topic>.md`)。规格+验收型人机回报
   闭环;防自欺条款(至少一条证伪型问题)。蒸馏(§4.1):十二维测试清单
   (process/quality/testing-checklist)+ 假设驱动试玩(philosophy/validation/
   iteration-and-validation)+ 难度试玩维度(systems/progression/difficulty-and-challenge)。
2. **bugfix 新写**(`roles/loop/bugfix.md`):按 DESIGN §6.3 详设写。九步流内嵌为
   BUG.md checklist;不开 HANDOFF、不走 review;两个升级出口(守护 role / 升级为
   feature)。蒸馏:九步 bug 流(process/workflow/bugfix-workflow)。
3. **release 新写**(`roles/ceremony/release.md`):按 DESIGN §6.4 详设写。十步发布
   流,人机回报闭环,产出 `harness/releases/<version>/RELEASE.md`。蒸馏:十步发布流
   (process/workflow/release-workflow)+ 版本与迁移判据(systems/operations/
   versioning-and-migration)。
4. **producer 移植修缮**(`roles/standing/producer.md`):底稿 =
   `G:/Claude/projects/Claude_Roles/roles/direction/` 下的 Producer 规范。修缮:
   统一契约段;INBOX 捕获行统一格式(§5.3);功能完成判据/归档义务对齐 HANDOFF v2;
   蒸馏(§4.1):里程碑=垂直切片判据(process/planning/roadmap-and-milestones)+
   backlog 管理(backlog-management)+ DoR 作进 Now 门槛(quality/definition-of-ready)。
5. 蒸馏方法照旧(并行子代理读理论、主会话装配);每写完 1-2 个 role commit 一次;
   批 4 全完后更新本文件状态行与「下一步」。

**批 3 实施备忘**(补充,批 4-5 保持一致):
- 引用格式约定(四守护 + kickoff 已统一,后续 role 沿用):引用其他 role 一律写
  `/g-xxx` 命令名,引用文件写反引号文件名,**不用 wikilink**。
- guardian 统一契约段的两处偏差(四份一致,视为 guardian 变体):artifact_location
  的 slug 规则改为"slug 可选,无 slug 即 cross-cutting";handoff duty 改为"有触发
  feature 则回写其 HANDOFF,否则在 harness/<dir>/ 顶部留一行索引"。
- 守护 role 专属蒸馏段命名:`<arch_judgment>` / `<balance_judgment>` /
  `<state_judgment>` / `<ux_judgment>`;kickoff 用 `<kickoff_judgment>`。
- ceremony 型(kickoff)无 slug、只产常驻文件;its signoff 指向 BOOTSTRAP 阶段 3
  首棒(/g-arch-guard),先打印完整仪式清单再给交棒行。
- ux-design 交互状态词汇最终以理论 Input Context 枚举为底(gameplay/menu/modal/
  targeting/text-entry/tutorial),加整理态(pause/cutscene/transition/loading/
  gameover,标 ※ 注明非原文枚举),替换 DESIGN 原拟的 default/focus 系词汇——
  与理论同源优先。

**批 2 实施备忘**(供后续批保持一致):
- role 文件 = 裸 XML 段,顺序照 role-template;蒸馏块放 `<theory>` 之后的
  role 专属段(`<design_judgment>` / `<planning_judgment>` / `<pitfall_index>` /
  `<review_axes>` / `<asset_discipline>` / `<import_discipline>` 这类命名)。
- design-jam / explorer 按 §4.1 不内嵌理论,`<theory>` 段里加了一行括号注明。
- reviewer 未引入 DoD 原文的 Done/Needs Fixes 判词,统一沿用 APPROVE 系 verdict,
  避免双词汇表。
- art-spec 占 HANDOFF 两行(美术规格/资源验收),ASSET-SPEC §4 改名 Generation spec
  (tool-ready,无下游编译器);integrator 收尾指向 /g-playtest(试玩默认 gate)。

## 已定的实施级小决策(DESIGN 之外)

- project-context §0 不复制 GAME-BRIEF 内容(支柱/范围唯一 owner 是 GAME-BRIEF),只放一句话 + 理论库路径。
- BOOTSTRAP 阶段 1 明确 GAME-BRIEF 不复制空模版(由 kickoff 产出);BACKLOG/STYLE-BIBLE/四事实源不手建。
- BUG/RELEASE/TUNE 的 frontmatter `feature:` 字段语义 = "所属工作单元"(bug slug / 版本号 / tune 编号)。
- 仓库文件用 LF(git 会警告 CRLF 转换,无害,忽略)。

## 未决 flags

- 无
