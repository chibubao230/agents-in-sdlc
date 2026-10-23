---
title: "第 3 课 - 智能体模式：Plan 和 Autopilot"
description: "使用 Plan 商定方案，使用 Autopilot 根据议题实现筛选功能，并在 Interactive 模式下审查结果。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

我们已从向项目添加一项小功能开始。不过，更大的更改需要更完善的流程。GitHub Copilot CLI 可以融入组织现有的工作流，帮助我们以正确的方式构建正确的功能。本课是若干课程中的第一课，你将遵循典型的智能体驱动开发流程：从议题着手实现新功能，确保代码有效、功能符合预期，最终成功合并到项目中。

> [!NOTE]
> 继续完成此功能时，将沿用同一对话和分支。实际工作中，不同类型的更改通常会使用不同分支或 PR；本研讨会为了聚焦核心概念，采用了简化流程。

本课将学习如何：

- 从 GitHub 议题开始新的 Copilot 对话。
- 在 Plan 模式中定义要求。
- 使用 Autopilot 模式实现新功能。
- 审查代码。
- 在转发的浏览器中手动验证功能。

继续实现此功能时，你还将更新存储库说明、自定义现有的 `quality-checks` 技能、添加 MCP 验证、创建 QA 智能体并创建功能 PR。

## 场景

Tailspin Toys 的游戏目录不断扩大，访客需要按类别和发行商筛选游戏。待办事项中的议题描述了这项功能，但在编写代码前，还需要商定类别组合等细节。你将使用 Plan 模式解决这些问题，然后授权 Autopilot 在明确范围内实现功能。

## 背景

将 AI 编码智能体引入开发流程，并不会改变软件开发的基本原则。事实上，这些原则变得更加重要！大多数开发人员遵循的流程大致如下：

1. 从已创建的议题开始，了解需要完成的工作。
2. 制定构建方案。
3. 编写并审查代码。
4. 运行测试以验证代码。
5. 手动验证新功能。
6. 创建拉取请求（PR）。
7. 代码通过审查且持续集成流程成功后，合并代码。

> [!NOTE]
> 具体流程会因团队和组织而异，但大多数流程都与上述内容类似。

遵循这一标准流程，可以确保 AI 生成的代码符合既定要求，并像手动编写的代码一样经过审核。

## 对话模式

**对话模式**控制智能体的自主程度。按 <kbd>Shift</kbd>+<kbd>Tab</kbd> 可在不同模式间切换：

- **Interactive**：你和智能体协同工作。智能体提出更改建议，并等待你输入后再继续。
- **Plan**：智能体先制定计划，但无法编辑项目文件。
- **Autopilot**：智能体自主工作——编写代码、运行测试并持续迭代，直到任务完成。

先从 Plan 模式开始并审查计划，然后使用 Autopilot 实现计划。

## 从议题开始

开始筛选功能之前，返回 Codespace 并确认存储库和终端已准备就绪。

1. 返回 Codespace。如果它已停止，请先重新启动。
2. 确认星级评分 PR 已合并。
3. 如果终端尚未打开，按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）。
4. 更新 `main`，然后为筛选功能创建分支：

   ```bash
   git checkout main
   git pull --ff-only
   git checkout -b game-filters-cli
   ```

5. 启动 Copilot CLI：

   ```bash
   copilot --yolo
   ```

6. 连按两次 <kbd>Tab</kbd>，打开 **Issues** 选项卡。
7. 按 <kbd>A</kbd> 显示所有议题。
8. 使用箭头键选中标题为 **Allow users to filter games by category and publisher** 的议题。
9. 按 <kbd>C</kbd> 将议题添加到提示中，并返回 **Session** 选项卡。

注意提示现在以 `#7`（或类似编号）开头。使用 `#` 可以将 GitHub 上的议题或拉取请求（PR）加入上下文。

## 规划筛选功能

规划可以让你在交由 Copilot 处理之前，先确定实现功能或执行任务的方法。对于复杂任务，花些时间规划总是有益的。现在切换到 Plan 模式，让 Copilot 制定计划。

1. 按 <kbd>Shift</kbd>+<kbd>Tab</kbd> 切换到 Plan 模式。确认提示下方的模式指示器显示 **Plan**。
2. 在上一步添加的议题引用后，输入以下提示：

   ```plaintext
   Create a plan for implementing this feature.
   ```

Copilot 会开始制定计划：先探索项目，再确定合适的方案。

3. Copilot 可能会询问筛选功能应如何工作。根据自己的偏好回答，这里没有错误答案！
4. 计划完成后，按 <kbd>Control</kbd>+<kbd>E</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>E</kbd>（Windows/Linux）展开计划。
5. 滚动查看计划。
6. 如果计划中的某些部分不符合你的决定，请让 Copilot 修改。

## 批准 Autopilot

计划编写并审查完成后，就可以开始实现了！现在使用 Autopilot，让 Copilot 开始工作。

Autopilot 会让 Copilot 持续处理问题，直到它认为任务已完成。

1. 选择 **Accept plan and build on autopilot (recommended)**，或你所用版本中名称相似的选项。
2. 确认提示下方的模式指示器显示 **Autopilot**。
3. 观察 Copilot 如何按照既定计划迭代、生成代码并运行测试。

> [!NOTE]
> 批准后可能会立即开始实现，因此请先审查计划。如果 Copilot 报告缺少依赖项或端口冲突，请先解决环境问题，再判断检查是否完成。

## 审查并验证实现

代码生成后，应像其他代码一样在合并前进行审查。现在一起审查代码并运行网站，确认一切正常。

1. 按 <kbd>Shift</kbd>+<kbd>Tab</kbd> 进入 Interactive 模式。确认模式指示器不再显示 **Plan** 或 **Autopilot**。
2. 输入 `/diff`，检查筛选功能的实现和测试。
3. 将结果与议题及规划时商定的内容进行比较。
4. 审查代码后，按 <kbd>Esc</kbd> 退出差异视图。
5. 查看项目检查的输出，并让 Copilot 解决任何失败项。

## 体验新功能

代码看起来没问题，但运行起来如何？像之前一样启动应用，并通过 Codespaces 转发的端口打开网站。

1. 让 Copilot 启动应用：

   ```plaintext
   Start the app so I can try the filtering feature in my browser. Tell me the URL and leave the server running.
   ```

2. Codespaces 报告端口 `4321` 可用时，选择 **Open in Browser**。
3. 尝试按类别筛选、按发行商筛选，以及计划中商定的筛选组合。
4. 确认重置和无结果时的行为符合议题要求和你的决定。
5. 返回 Codespace，让 Copilot 停止刚才启动的开发服务器。

## 总结和后续步骤

你已使用不同的对话模式来构建和审查功能。本课中，你：

- 从 GitHub 议题开始了新的 Copilot 对话。
- 在 Plan 模式中定义了要求。
- 使用 Autopilot 模式实现了新功能。
- 审查了代码。
- 在转发的浏览器中手动验证了功能。

接下来，[使用自定义说明][next-lesson]，进一步了解代码生成，并确保代码遵循已记录的实践。

## 资源

- [GitHub Copilot CLI 中的 Autopilot][autopilot]
- [Copilot CLI 命令参考][cli-reference]

[autopilot]: https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[previous-lesson]: ../2-add-star-rating/
[next-lesson]: ../4-custom-instructions/
