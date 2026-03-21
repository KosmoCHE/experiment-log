---
name: archive
description: 归档当前实验，询问是否合并实验分支
allowed-tools: Read, Write, Edit, Glob, Bash
disable-model-invocation: true
---

归档当前科研实验，将记录存入 experiments/ 子目录。

## 前置检查

1. 读取 `.claude/project.md` 和 `.claude/plan.md`
   - 若无进行中的实验（仍为初始占位内容），告知用户并退出

## 向用户确认

逐项询问：
1. 实验状态：**已完成** 还是 **已中止**？
2. 请总结实验结论（一两句话即可）
3. 验收标准的完成情况（展示 project.md 中的标准列表，逐项确认）

## 归档操作

1. 从 project.md 提取实验编号和名称（如 exp-001 对比学习率影响）

2. 创建 `.claude/experiments/exp-XXX-<name>/README.md`：

```markdown
# exp-XXX <实验名称>

**状态：** 已完成 / 已中止
**日期：** YYYY-MM-DD ~ YYYY-MM-DD
**分支：** exp/<编号>-<名称简写>

## 实验方案
<从 plan.md 提取步骤和技术方案>

## 实验结果
<从实际执行和用户反馈中总结>

## 结论
<用户提供的结论>

## 验收情况
<从 project.md 提取验收标准及完成状态>
```

3. 更新 `.claude/experiment-log.md` 中对应实验行：
   - 状态改为 `已完成` 或 `已中止`
   - 填入结束日期和简要结论

4. 重置 `.claude/project.md` 为：
```
# 当前实验

> 暂无进行中的实验。使用 `/experiment-log:new <实验名称>` 开始新实验。
```

5. 重置 `.claude/plan.md` 为：
```
# 执行计划

> 暂无进行中的计划。
```

## Git 分支处理

归档完成后：

1. 检查当前是否在实验分支上（分支名以 `exp/` 开头）
2. 若在实验分支上，询问用户：
   - **是否要将此实验分支合并到主分支？**
   - 用户选择合并：
     a. 确认主分支名称（main 或 master）
     b. 切换到主分支
     c. 执行 `git merge <实验分支>`
     d. 合并成功后，询问是否删除实验分支
   - 用户选择不合并：
     a. 切换回主分支
     b. 保留实验分支不删除
3. 若不在实验分支上（或不是 git 仓库），跳过

## 完成

展示归档摘要：实验名称、状态、结论、归档路径。
提示使用 `/experiment-log:new <名称>` 开始下一个实验。
