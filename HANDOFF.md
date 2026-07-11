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
| 3 守护型 + kickoff | arch-guard / num-smith / state-machine / ux-design 移植修缮;kickoff 新写 | [ ] |
| 4 回路 + 仪式(下)+ 常驻 | playtest / bugfix / release 新写;producer 移植修缮 | [ ] |
| 5 命令接线 | 拷入 `~/.claude/skills/g-<name>/`;玩具项目端到端验证 | [ ] |

## 下一步

**批 3:守护型 role 移植修缮 + kickoff 新写。** 逐个流程:

1. **四守护 role**(`roles/guardian/`):底稿在 `G:/Claude/projects/Skills/` 下的
   arch-guard / num-smith / state-machine-master / ux-design 四个 SKILL.md。
   统一契约段用 `role-template.md` 母版(guardian 的 `<work_mode>` 含模式 A/B/逆推);
   §5.4 修缮:四守护输入对称(arch-guard 补读 FEATURE-DESIGN);重叠定 owner
   (转移表归 STATE-MACHINES,UX-MAP 只持屏幕清单;存档格式归 ARCHITECTURE 单一持有);
   ux-design 交互状态词汇游戏化(default/focus/pause/transition/cutscene/gameover);
   wikilink 等格式约定四 role 统一定一种;产物名对齐 `<KIND>-CHANGE-<NN>-<slug>`
   (arch-guard 弃 REFACTOR 名)。
2. **理论蒸馏**(§4.1):arch-guard = solid-in-godot + 状态所有权/依赖方向/故障边界
   三原则 + 存档归属;num-smith = 资源流闭合六要素 + reward/difficulty;
   state-machine = 十类 FSM 分类坐标系(fsm-index)+ game-state-and-flow;
   ux-design = input-and-interaction + settings/tutorial/notification 三篇。
   批 2 的蒸馏方法可复用:并行子代理读理论、返回可执行形态,再由主会话装配。
3. **kickoff 新写**(`roles/ceremony/kickoff.md`):无底稿,按 DESIGN §6.1 详设写;
   蒸馏 = 核心体验框架 + 目标玩家问题组 + 默认优先级/冲突流程 + 证据等级 +
   v1 最小系统集裁剪(philosophy/foundation 三篇、conflict-resolution、system-map)。
4. 每写完 1-2 个 role commit 一次;批 3 全完后更新本文件状态行与「下一步」。

**批 2 实施备忘**(供批 3 保持一致):
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
