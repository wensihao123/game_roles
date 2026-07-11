# BOOTSTRAP — 新游戏项目铺地基 checklist

> 一次性、按顺序勾完。阶段 0-1 是纯人工的机械动作;阶段 2 起进入 role 流程。
> 全部勾完 = 立项期结束,可以开第一个功能。

## 阶段 0:建工程

- [ ] 建 Godot 工程(版本写进心里,阶段 2 要填);`git init`,首 commit。
- [ ] `.gitignore`:`.godot/` 排除;资源旁 `*.import` **要**提交。
- [ ] 建 `harness/` 目录。

## 阶段 1:复制骨架(从本仓库 `templates/`)

- [ ] `project-context.md` → `harness/project-context.md`(先不填,阶段 2 由 kickoff 代填草稿)
- [ ] `GAME-BRIEF.md` → 不复制——它由 `/g-kickoff` 直接产出,复制空模版反而误导。
- [ ] `INBOX.md` → `harness/INBOX.md`
- [ ] `style-basic-2d.md` → `harness/style-basic-2d.md`(替换其中的尖括号占位符可留到 art-spec 首跑)
- [ ] `HANDOFF.md` → `harness/templates/HANDOFF.md`(**注意是项目内 templates/ 子目录**,
      供每次开新功能时复制;放错到 harness 顶层会被当成活文件)
- [ ] `BUG.md` → `harness/templates/BUG.md`;`RELEASE.md` → `harness/templates/RELEASE.md`
- [ ] 建空目录:`harness/features/`、`harness/bugs/`、`harness/releases/`、
      `harness/playtest/`、`harness/archive/`、`harness/{arch,balance,state,ux}/`
- [ ] **不要手建** BACKLOG.md、STYLE-BIBLE.md、四事实源——它们分别由 producer、art-spec、
      四守护 role 首跑时产出,手建空壳会让首跑误判"已有现状"。

## 阶段 2:立项仪式

- [ ] `/g-kickoff`,带着你的游戏点子(一句话到一页纸都行)。
      产出:`harness/GAME-BRIEF.md`(含支柱、v1 范围、最小系统集、风险清单)
      + `harness/project-context.md` 第 0 节草稿。
- [ ] 人工补完 project-context 其余各节:引擎版本(写准!integrator 按它给步骤)、
      目录约定、代码约定、hard NOs、验证流程。
- [ ] project-context §0 填**理论库根目录**(GameDesignDocs 路径;不用理论库就留空)。

## 阶段 3:事实源开局 + 初版 BACKLOG

按 GAME-BRIEF 的**最小系统集**逐个开局(裁掉的系统群不建对应条目):

- [ ] `/clear` → `/g-arch-guard`(模式 A:读 GAME-BRIEF,定初版数据模型/模块边界/不变量 → ARCHITECTURE.md)
- [ ] `/clear` → `/g-state-machine`(模式 A:app/game 流程 FSM 起步 → STATE-MACHINES.md)
- [ ] `/clear` → `/g-ux-design`(模式 A:屏幕清单与每屏职责起步 → UX-MAP.md)
- [ ] `/clear` → `/g-num-smith`(模式 A:数值哲学与核心属性起步 → BALANCE.md;
      纯无数值的游戏可跳过,在 GAME-BRIEF 系统集注明)
- [ ] `/clear` → `/g-producer`(读 GAME-BRIEF → 初版 BACKLOG.md:v1 scope 一句话 + Now/Next/Later)

## 阶段 4:第一个功能走通全链

- [ ] 挑 GAME-BRIEF 风险清单里最该验证的那个假设对应的功能(不是最有把握的功能)。
- [ ] `harness/features/01-<slug>/` 建目录,从 `harness/templates/HANDOFF.md` 复制一份进去。
- [ ] 走链:(`/g-design-jam 01-<slug>` 可选)→ `/g-designer` → `/g-planner` →
      `/g-implementer` → `/g-reviewer` →(`/g-art-spec`/`/g-integrator` 视需要)→
      `/g-playtest`。每棒之间 `/clear`,照抄交棒行即可。
- [ ] 功能完成 → `/clear` → `/g-producer` 归档 + 记 Shipped。

## 立项期完成判据(五件套)

- [ ] `harness/GAME-BRIEF.md` 存在且六节填实(支柱每条带牺牲面,系统集逐群裁定)
- [ ] `harness/project-context.md` 全节填实(无尖括号占位符残留)
- [ ] 最小系统集对应的事实源已建(ARCHITECTURE 必有,其余按裁定)
- [ ] `harness/BACKLOG.md` 有 v1 scope 一句话 + 非空 Now
- [ ] 第一条功能链已走通一遍(阶段 4,验证的是**流程**本身)
