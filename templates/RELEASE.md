---
artifact: RELEASE
feature: v0.0                  # 版本号,= releases/ 下目录名
role: release
status: draft                  # draft(进行中)| accepted(已发布)| blocked
updated: YYYY-MM-DD
inputs: [BACKLOG.md]           # + 上一版 releases/<prev>/RELEASE.md(若有)
next: 用户
---
# RELEASE — <版本号>

> 放在 `harness/releases/<version>/RELEASE.md`,**单文件走完全程**,十步 checklist
> 就是发布管线。release role 出清单与判据,**导出/上传/真机验证由你执行并回报**,
> 三态记号驱动,全部 `[x]` 才判 Released。发布后本目录原地保留作账本。

## 本版内容

- 收入的功能(来自 BACKLOG Shipped 区,逐条列 slug):<…>
- 收入的 bug 修复(bugs/ 中本周期关闭的):<…>

## 发布 checklist(十步,三态记号驱动)

- [ ] **1. 功能冻结确认**:Now 区无未完结功能,或已明确排除到下版;冻结起点 commit:<hash>
- [ ] **2. 回归范围裁定**:必测 <本版新功能全部 + 从 bugs/ 历史挑出的高危区(改过存档/输入/场景切换的)>;
      免测理由记录:<…>
- [ ] **3. 存档兼容**:老版本存档在本版可读/迁移;迁移逻辑测试:<结果>。
      (存档格式的唯一权威是 ARCHITECTURE.md,有变更须先过 arch-guard。)
- [ ] **4. 导出测试**:headless 导入检查(`godot --headless --import`)无错;
      Export 预设逐平台导出成功;**你在真机跑导出包**(不是编辑器 F5)回报结果:<…>
- [ ] **5. 版本号与 changelog**:版本号 <遵循 project-context 约定>;changelog 草稿见下节。
- [ ] **6. 构建**:最终构建产物 <路径/命名>;构建 commit 打 tag:<tag>
- [ ] **7. 上架/分发**:<itch/Steam/自分发> 上传步骤逐项执行并回报:<…>
- [ ] **8. 发布后验证**:下载线上包安装运行;首屏/存档/核心循环各验一遍:<结果>
- [ ] **9. 回滚预案**:若线上炸了,回滚到 <上一版 tag>;回滚操作步骤:<…>
- [ ] **10. 收尾**:BACKLOG Shipped 区为本版功能标记版本号;通知 producer 归档;
      frontmatter `status: accepted`。

## Changelog 草稿

```
<version> — YYYY-MM-DD
新增:
- <玩家能感知的说法,不是 commit message>
修复:
- <…>
调整:
- <…>
```

## 账本(按需读)

- <发布过程中的问题与教训;可自动化的步骤记进 INBOX 交 producer>
