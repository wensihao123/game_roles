---
artifact: BUG
feature: NN-slug               # bug slug,= bugs/ 下目录名,如 03-wall-stuck
role: bugfix
status: draft                  # draft(处理中)| accepted(已关闭)| blocked
updated: YYYY-MM-DD
inputs: []                     # 实际读过的文件(复现涉及的场景/脚本、相关事实源)
next: 用户                     # 通常是"用户验证修复";升级时写对应 role
---
# BUG — <一句话现象>

> 放在 `harness/bugs/<NN-slug>/BUG.md`,**单文件走完全程**:不开 HANDOFF、不走 review,
> 九步 checklist 就是它的管线。轻量通道的两条铁律:
> **① 修复前必须有失败复现,修复后必须有通过验证,都记录在案;**
> **② 触发升级出口就立刻停,不在轻量通道里硬扛重活。**
> bug 关闭(status: accepted)后本目录原地保留作账本,role 默认不扫。

## 0. 现象

- 观察到什么:<用户原话 + 补充观察>
- 首次发现:<日期 / 场景 / 版本>

## 处理 checklist(九步,三态记号驱动)

- [ ] **1. 复现**:步骤 <1→2→3>,复现率 <必现/概率约N%>。
      拿不到稳定复现 → STOP,向用户要更多信息,别猜着修。
- [ ] **2. 严重度与影响面**:<crash/卡死/数值错/表现瑕疵>;影响 <哪些功能/场景/存档>。
- [ ] **3. 根因**(证据先于修复,禁止猜改):
      - 证据:<日志/断点/最小复现工程的观察>
      - 根因:<一句话,指向具体代码/配置/资源>
- [ ] **4. 最小修复**:<改了什么文件的什么;为什么这是最小改法>
- [ ] **5. 回归验证**:原复现步骤跑 <N> 次全过;<相关自动化测试命令> 全绿。
- [ ] **6. 相邻系统检查**:<根因所在模块的邻居们(信号接收方/同 autoload 消费者/同场景兄弟节点)抽查结果>
- [ ] **7. 要不要补自动化测试**:<要:补了什么 / 不要:为什么(如纯表现类)>
- [ ] **8. 要不要更新文档/事实源**:<涉及 ARCHITECTURE/BALANCE/STATE-MACHINES/UX-MAP 或
      project-context「已知的坑」的,更新了哪处 / 不涉及>
- [ ] **9. 收尾**:用户确认修复 → frontmatter `status: accepted`;INBOX 若有本 bug 的
      衍生想法已记录;交棒行打印。

## 升级出口(触发即停,本文件记录去向)

- **根因牵扯架构/数值/状态机/交互结构** → 停,记根因证据,交棒
  `/g-arch-guard` / `/g-num-smith` / `/g-state-machine` / `/g-ux-design`,
  本 BUG `status: blocked`,等 CHANGE 方案落地后回来收尾。
- **修复超出"最小修复"**(要动多文件/改接口/新增模块)→ 停,升级为正式 feature:
  在 INBOX 记一行(注明源自本 bug),交 producer 记账排期,本 BUG `status: blocked`
  并在此注明升级去向。

## 账本(按需读)

- <多轮尝试的失败记录、被推翻的假设,压缩着写;当前真相在上面 checklist 里>
