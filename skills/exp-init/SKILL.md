---
name: exp-init
description: 初始化科研实验项目的 .claude/ 文档结构
allowed-tools: Read, Write, Edit, Glob, Bash
disable-model-invocation: true
---

在当前项目中初始化科研实验管理结构。

## 前置检查

1. 检查 `.claude/experiment-log.md` 是否已存在
   - 若已存在，告知用户结构已初始化，展示当前状态后退出
   - 若不存在，继续

## 创建文件

创建 `.claude/experiments/` 目录（`mkdir -p`），然后创建以下文件：

### `.claude/CLAUDE.md`

```
# 实验项目规则

本项目使用 .claude/ 目录管理科研实验。

## 文件职责
- `project.md`：当前实验的验收大纲（目标、预期结果、验收标准）—— 修改需经用户确认
- `plan.md`：当前实验的执行计划（步骤、进度、技术细节）—— AI 自主维护
- `experiment-log.md`：所有实验的概述索引
- `experiments/exp-XXX-<name>/README.md`：已归档实验的完整记录

## 维护规则
1. 执行实验步骤时，及时更新 plan.md 中的进度（用 checkbox 标记）
2. 实验完成后，提醒用户运行 /experiment-log:exp-archive 归档
3. 不要修改 experiments/ 下已归档的文件，除非用户明确要求
4. project.md 的内容变更需向用户确认后再写入；plan.md 由 AI 自主更新
```

### `.claude/project.md`

```
# 当前实验

> 暂无进行中的实验。使用 `/experiment-log:exp-new <实验名称>` 开始新实验。
```

### `.claude/plan.md`

```
# 执行计划

> 暂无进行中的计划。
```

### `.claude/experiment-log.md`

```
# 实验日志

| 编号 | 名称 | 状态 | 起止日期 | 简要结论 |
|------|------|------|----------|----------|
```

## Git 分支处理

创建完文件后：

1. 运行 `git rev-parse --is-inside-work-tree` 检查是否在 git 仓库内
2. 若是 git 仓库：
   - 询问用户是否要为实验项目创建专用基础分支（如 `experiment/main`）
   - 用户同意则创建并切换
   - 用户拒绝则跳过
3. 若不是 git 仓库，跳过分支操作

## 完成

展示创建的文件列表，提示使用 `/experiment-log:exp-new <名称>` 开始第一个实验。
