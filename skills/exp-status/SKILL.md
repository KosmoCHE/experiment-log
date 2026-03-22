---
name: exp-status
description: 查看当前实验状态和历史实验概览
allowed-tools: Read, Glob, Bash
disable-model-invocation: true
---

展示当前科研实验的状态信息。

## 前置检查

确认 `.claude/experiment-log.md` 存在，若不存在提示运行 `/experiment-log:exp-init`。

## 展示内容

### 1. 当前实验

读取 `.claude/project.md`：
- 若有进行中的实验：展示实验名称、目标、验收标准完成度
- 若无：提示暂无进行中的实验

读取 `.claude/plan.md`：
- 若有执行计划：统计步骤完成进度（已勾选 / 总数），展示未完成步骤
- 若无：跳过

### 2. 实验历史

读取 `.claude/experiment-log.md`，展示：
- 实验总数、已完成数、已中止数、进行中数
- 完整的实验日志表格

### 3. Git 信息

若为 git 仓库（`git rev-parse --is-inside-work-tree`）：
- 当前分支名
- 是否有未提交的更改（`git status --porcelain` 是否有输出）
- 列出所有实验分支（`git branch --list 'exp/*'`）

### 输出格式

用简洁的格式输出，例如：

```
当前实验：exp-003 对比正则化方法
   进度：3/5 步骤已完成
   分支：exp/003-regularization

实验历史：共 3 个（2 已完成，0 已中止，1 进行中）

Git：分支 exp/003-regularization，有未提交更改
```
