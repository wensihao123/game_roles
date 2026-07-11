---
updated: YYYY-MM-DD
---
# Project Context (shared by all roles)

> 这是所有 role 的"共享内存"之一(另一份是 GAME-BRIEF.md)。每个 session 都应先读这两份。
> 分工:**GAME-BRIEF 回答"做什么游戏、做到哪为止"(它是支柱与范围的唯一权威);
> 本文件回答"这个项目怎么施工"**。保持简短、事实化、可信——它一错,所有 role 跟着错。
>
> 用法:`/g-kickoff` 立项时会代填第 0 节草稿;其余各节由你补实。所有占位符都要替换。

## 0. 游戏与理论库

- 游戏一句话:<与 GAME-BRIEF §1 一致;支柱、v1 范围**只在 GAME-BRIEF 维护**,这里不复制>
- 理论库(GameDesignDocs)根目录:<绝对路径,如 G:/Codes/GameDesignDocs;
  留空则各 role 的"按需下钻"自动跳过,只用 spec 内嵌的蒸馏层>

## 1. 引擎与技术栈

- 引擎 + 版本:Godot <e.g. 4.4 stable>  ← 务必写准,integrator 按这个版本给步骤
- 脚本语言:<GDScript / C#>
- 目标平台:<e.g. PC (Windows/Linux), itch.io>
- 美术风格基线:<e.g. 像素风,基准分辨率 320x180,整数缩放>
- 测试:<e.g. gdUnit;命令 ...>
- 其它工具:<lint / format / 静态检查命令>

## 2. 目录约定

```
<game-project>/          [Godot 项目根,= res://]
  project.godot
  src/                    [脚本]
  scenes/                 [.tscn]
  assets/                 [运行时素材;源文件进 _source/(.gdignore)]
  harness/                [role 的 artifact,纳入版本控制]
    GAME-BRIEF.md         [立项承诺,kickoff 产出]
    project-context.md    [本文件]
    BACKLOG.md            [producer]
    INBOX.md              [中途捕获队列]
    STYLE-BIBLE.md        [art-spec]
    style-basic-2d.md     [2D 图形工程纪律,复制自规范仓库]
    ARCHITECTURE.md  BALANCE.md  STATE-MACHINES.md  UX-MAP.md   [四事实源]
    arch/ balance/ state/ ux/   [各类 CHANGE 文档]
    features/<NN-slug>/   [每功能一目录 + HANDOFF.md]
    bugs/<NN-slug>/       [BUG.md 轻量通道]
    releases/<version>/   [RELEASE.md 每版本一份]
    playtest/             [TUNE-<NN> 整体调优记录]
    templates/            [HANDOFF 模版副本,供开新功能时复制]
    archive/              [done 的功能整目录挪入]
```

## 3. 代码约定

- 命名:<e.g. 文件 snake_case,节点 PascalCase,signal 过去式 died/jumped>
- 风格:<e.g. 优先 composition over inheritance;早返回>
- 信号 vs 直调:<e.g. 跨系统用 signal,父子内部可直调>
- 注释:<e.g. 只在"为什么"不显然时注释>

## 4. 禁止事项(hard NOs)

- <e.g. 不引入新插件/AddOn,除非计划明确批准>
- <e.g. 密钥/路径不硬编码>
- <e.g. 不做计划外的"顺手重构"或"顺手加功能">

## 5. 验证一次改动是否 OK 的标准流程

按顺序,全绿才算通过:

```
<e.g. godot --headless --import 无报错>
<e.g. 跑测试命令>
<e.g. 手动:打开 X 场景按 Play,观察 Y>
```

## 6. 当前已知的坑 / 临时约束

- <e.g. 输入系统正在重构,别动 input_manager>
- <e.g. 某 autoload 还没建,依赖它的功能要先 flag>
