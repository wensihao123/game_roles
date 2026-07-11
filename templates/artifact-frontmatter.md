# Artifact Frontmatter 约定

每个**工作单元级** artifact 文件**开头**都带这段 YAML frontmatter,让任何冷启动的
session 一眼知道来历、状态、下一棒交给谁:

```yaml
---
artifact: PLAN                 # 类型,见下方全量枚举
feature: 01-double-jump        # 所属工作单元(见下方语义)
role: planner                  # 产出这份 artifact 的 role
status: draft                  # draft | accepted | superseded | blocked
updated: 2026-07-11            # 最后更新日期
inputs: [FEATURE-DESIGN.md, CONTEXT-FINDINGS.md]   # 实际消费的上游 artifact
next: implementer              # 下一棒应由谁接手(role 名或"用户")
---
```

## `artifact:` 全量枚举

| 类型 | 产出 role | 所在位置 |
|------|----------|----------|
| IDEA | design-jam | `features/<NN-slug>/` |
| FEATURE-DESIGN | designer | `features/<NN-slug>/` |
| CONTEXT-FINDINGS | explorer | `features/<NN-slug>/` |
| PLAN | planner | `features/<NN-slug>/` |
| CHANGES | implementer | `features/<NN-slug>/` |
| REVIEW | reviewer | `features/<NN-slug>/` |
| ASSET-SPEC | art-spec | `features/<NN-slug>/` |
| ACCEPTANCE | art-spec | `features/<NN-slug>/` |
| INTEGRATION-STEPS | integrator | `features/<NN-slug>/` |
| PLAYTEST | playtest | `features/<NN-slug>/` |
| BUG | bugfix | `bugs/<NN-slug>/` |
| RELEASE | release | `releases/<version>/` |
| TUNE | playtest(整体调优模式) | `playtest/` |
| ARCH-CHANGE | arch-guard | `arch/` |
| BALANCE-CHANGE | num-smith | `balance/` |
| STATE-CHANGE | state-machine | `state/` |
| UX-CHANGE | ux-design | `ux/` |

(位置均相对游戏项目的 `harness/`。)

## 字段语义

- `feature`:**所属工作单元**——per-feature artifact 填功能 slug;BUG 填 bug slug;
  RELEASE 填版本号;TUNE 填 tune 编号;CHANGE 系列填 `<NN>-<slug>` 编号。
- `status`:`draft` 刚产出待验收;`accepted` 人类/下游已采纳;`blocked` 卡在某个
  flag;`superseded` 被新版本取代(保留留痕,不删)。
- `inputs`:写你**真的读过并据以决策**的上游文件,不是"理论上相关"的。
- `next`:配合 HANDOFF 的"下一步",让交接无需口头说明。

## 两个特例

1. **HANDOFF.md** 用精简三字段(它是状态看板,不是接力产物):
   ```yaml
   ---
   feature: NN-slug
   status: in-progress        # backlog | in-progress | done | parked
   updated: YYYY-MM-DD
   ---
   ```
2. **常驻文件**不走 per-unit frontmatter,只保留一行 `updated: YYYY-MM-DD`。
   常驻文件的**统一全量清单**(所有 role 的 spec 引用同一份,不得各自增删):
   `project-context.md`、`GAME-BRIEF.md`、`BACKLOG.md`、`INBOX.md`、`STYLE-BIBLE.md`、
   `style-basic-2d.md`、`ARCHITECTURE.md`、`BALANCE.md`、`STATE-MACHINES.md`、`UX-MAP.md`。

## 状态 vs 历史(防上下文噪音)

项目跑久了,文件会把「当前状态」和「历史账本」混在一起,role 整篇读就吸进噪音。
三条硬约定:

1. **状态在前、历史在后/外;读取时先看状态,需要历史才往下钻。**
   - **状态**=回答"现在是什么样、下一步做什么"的部分,小而新,role 必读:
     HANDOFF 的「管线状态 + 下一步」、BACKLOG 的「v1 scope + Now/Next」、GAME-BRIEF、
     事实源的当前形态。
   - **历史/账本**=只增的留痕,默认不读、要追溯才读:HANDOFF 的「决策记录」、
     BACKLOG 的「Decision log / Cut」、各 `*-CHANGE-NN.md`、CHANGES.md、BUG/RELEASE/TUNE
     的历史目录。把它们放在文件底部或单独文件里,别插在状态前面。
   - 写状态部分时**就地更新**(覆盖旧值),不要用追加日志的方式记当前状态。
   - 历史部分定期滚动收口:陈旧、不再有约束力的条目压成一句话摘要或随功能归档。
2. **活的事实源只写当前真相。** ARCHITECTURE.md / BALANCE.md / STATE-MACHINES.md /
   UX-MAP.md 只描述"现在长什么样",**不在里面记变更史**;每次变更的诊断与取舍归各自的
   `harness/{arch,balance,state,ux}/*-CHANGE-NN.md`。事实源要小到能一眼看清现状。
   GAME-BRIEF 同理:立项后它是"当前承诺",支柱/范围变更走 Producer 决策 + 就地更新。
3. **工作单元完结后归档。** 功能 `done` 后 `harness/features/<NN-slug>/` 整目录挪到
   `harness/archive/<NN-slug>/`;bug 关闭、版本发布后,`bugs/<NN-slug>/`、
   `releases/<version>/` 原地保留(它们本身就是账本,role 默认不扫)。role 默认只扫
   `harness/features/`(活动中),归档的出读取范围、仍在版本控制里。详见 README「防上下文噪音」。
