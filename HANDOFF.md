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
| 2 管线型 role | design-jam / designer / explorer / planner / implementer / reviewer / art-spec / integrator | [ ] |
| 3 守护型 + kickoff | arch-guard / num-smith / state-machine / ux-design 移植修缮;kickoff 新写 | [ ] |
| 4 回路 + 仪式(下)+ 常驻 | playtest / bugfix / release 新写;producer 移植修缮 | [ ] |
| 5 命令接线 | 拷入 `~/.claude/skills/g-<name>/`;玩具项目端到端验证 | [ ] |

## 下一步

**批 2:写 `roles/pipeline/` 下 8 个管线型 role。** 逐个流程:

1. **移植底稿**:旧体系对应文件在 `G:/Claude/projects/Claude_Roles/roles/`
   (design-jam 在 `G:/Claude/projects/Skills/design-jam/SKILL.md`)。
2. **换统一契约段**:以本仓库 `role-template.md` 为准——`<language>` `<activation_handshake>`
   `<artifact_location>` `<theory>` `<mid_flow_capture>` `<handoff_signoff>` 六段直接用母版
   (只改 role 名),**不要沿用旧文件里的同名段落**(旧版有 token 漂移和枚举不全)。
3. **修缮**:对照 DESIGN §5.3/§5.4 逐条——常驻文件统一清单;INBOX 行用 `[来自 …]`;
   交棒行短格式;planner 必读四事实源+四路由;explorer 四事实源列可选输入;
   implementer escalation 补全四守护路由;reviewer 评审轴并入 review-checklist 维度;
   art-spec 链路无 image-prompt(ASSET-SPEC 直接可喂外部生图工具,风格一致性引 STYLE-BIBLE);
   HANDOFF 行名对齐 v2 十行表(美术规格/资源验收分行)。
4. **理论蒸馏**:按 DESIGN §4.1 映射表逐 role 内嵌,每段标
   `蒸馏自 GameDesignDocs/<path> @ 2026-07`;理论库在 `G:/Codes/GameDesignDocs`,
   蒸馏前读对应篇目(蒸的是可执行形态:清单/七问/路由表,不是原文摘抄)。
5. 每写完 1-2 个 role commit 一次;批 2 全完后更新本文件状态行与「下一步」。

## 已定的实施级小决策(DESIGN 之外)

- project-context §0 不复制 GAME-BRIEF 内容(支柱/范围唯一 owner 是 GAME-BRIEF),只放一句话 + 理论库路径。
- BOOTSTRAP 阶段 1 明确 GAME-BRIEF 不复制空模版(由 kickoff 产出);BACKLOG/STYLE-BIBLE/四事实源不手建。
- BUG/RELEASE/TUNE 的 frontmatter `feature:` 字段语义 = "所属工作单元"(bug slug / 版本号 / tune 编号)。
- 仓库文件用 LF(git 会警告 CRLF 转换,无害,忽略)。

## 未决 flags

- 无
