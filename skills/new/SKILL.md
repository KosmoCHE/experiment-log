---
name: new
description: 开始一个新的科研实验，创建实验分支并初始化文档
allowed-tools: Read, Write, Edit, Glob, Bash
disable-model-invocation: true
argument-hint: "<实验名称>"
---

开始一个新的科研实验。

## 前置检查

1. 确认 `.claude/experiment-log.md` 存在，若不存在提示先运行 `/experiment-log:init`
2. 读取 `.claude/project.md`，检查是否有进行中的实验（不是初始占位内容）
   - 若有，提醒用户先运行 `/experiment-log:archive` 归档当前实验
   - 用户明确要放弃当前实验才可继续

## 操作步骤

1. 从 `$ARGUMENTS` 获取实验名称，若为空则询问用户

2. 读取 `.claude/experiment-log.md` 确定下一个编号（exp-001, exp-002, ...），编号三位补零

3. **Git 分支**：
   - 检查是否为 git 仓库
   - 若是，从当前分支创建并切换到新分支：`exp/<编号>-<名称简写>`
     - 名称简写：取英文关键词，用连字符连接，全小写，不要使用拼音
     - 例：`exp/001-learning-rate`、`exp/002-batch-size`
   - 若不是 git 仓库，跳过

4. 向用户询问以下信息（逐项确认）：
   - 实验目标是什么？
   - 假设 / 预期结果是什么？
   - 主要修改了哪些部分？
   - 代码运行流程是怎样的？（数据从哪里进、经过哪些处理、输出什么）
   - 验收标准有哪些？

5. 根据用户回答更新 `.claude/project.md`：

```markdown
# 当前实验：exp-XXX <实验名称>

## 目标
<用户描述>

## 假设 / 预期结果
<用户描述>

## 主要修改
- <修改了哪个文件/模块，做了什么改动>

## 运行流程
<代码的执行流程：入口 → 数据加载 → 处理/训练 → 输出结果，描述关键步骤和模块间的调用关系>

## 验收标准
- [ ] <标准1>
- [ ] <标准2>
```

6. AI 自主规划并更新 `.claude/plan.md`：

```markdown
# 执行计划：exp-XXX <实验名称>

## 步骤
- [ ] 步骤1：<描述>
- [ ] 步骤2：<描述>

## 技术备注
<实现细节、依赖、注意事项>
```

7. 在 `.claude/experiment-log.md` 表格末尾添加一行：
   - 编号、名称、状态=`进行中`、起始日期=今天（YYYY-MM-DD）、结论=空

8. 完成后展示实验概要，提示用户可以开始实验工作。
