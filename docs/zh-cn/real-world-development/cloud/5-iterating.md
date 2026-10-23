---
title: "练习 5：迭代 GitHub Copilot 的工作"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

## 审查工作

在本实验的各项练习中，你使用 GitHub Copilot 处理了多项改善用户体验和添加功能的任务。你让 Copilot 为代码添加文档、实现供设计团队迭代的相关游戏功能，并添加包含高对比度和浅色模式切换的无障碍功能。现在检查这些代码更改，必要时向 Copilot 提供反馈以改进其工作。

### 场景

如前所述，加入生成式 AI 并不会改变软件设计和 DevOps 的基本原则。我们始终需要审查生成的代码，并遵循正常的 DevOps 流程。因此，在为团队其他成员启用代码审查前，先审查 GitHub Copilot 对文档、相关游戏功能和无障碍功能的建议。

## 安全性和 GitHub Copilot 云智能体

Copilot 云智能体会异步且无人监督地执行任务，因此需要遵循一些安全限制，以确保操作安全：

- Copilot 对存储库只有读取权限，并且**只能**对用于编写代码的分支拥有写入权限。
- 云智能体在 GitHub Actions 中运行，并在其中创建独立的临时环境进行工作。
- 任何 GitHub Actions 工作流都必须经人工批准后才能运行。
- [默认情况下会限制对外部资源的访问](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)，包括 MCP 服务器。

## 审查生成的文档

先查看 GitHub Copilot 云智能体创建的第一个拉取请求（PR），它为代码添加了文档。使用 GitHub.com 上的标准 PR 界面完成审查。

> [!NOTE]
> 查看 PR 时，可能会看到 GitHub Copilot 被防火墙阻止的警告。这是**预期行为**，因为 Copilot 默认只能有限访问外部资源，包括调用外部 MCP 服务器。如有需要，可以[自定义或禁用 Copilot 云智能体的防火墙][agent-firewall]。

1. 返回 github.com 上的存储库。
2. 选择 **Pull Requests**，打开 PR 列表。
3. 打开标题类似于 **Add missing documentation** 的拉取请求。

> [!NOTE]
> 如果 Copilot 仍在处理任务，拉取请求会带有 **[WIP]** 标记。此时请等待 Copilot 完成工作，这可能需要几分钟。你可以休息一下，或回顾目前学到的内容。

4. PR 准备就绪后，选择 **Files changed** 选项卡并审查更改。

    ![Files changed 选项卡](../../../_images/shared-pr-files-changed.png)

5. 查看更新后的代码，其中包含新添加的 TSDoc 文档注释和其他文档。具体更改可能有所不同。

    检查更改时，留意 TSDoc 文档注释、TypeScript 约定和注释标头。这些内容来自你在练习 2 开始时查看的自定义说明文件。

6. 审查更新并确认没有问题后，返回 **Conversation** 选项卡并向下滚动。
7. 确认页面显示有工作流等待批准。
8. 如果工作流正在等待批准，请选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. PR 的检查部分应显示工作流已排队。如果一切正常，单个 Astro 应用的项目检查将通过。这可能需要几分钟。

## 请求 GitHub Copilot 修改

与 Copilot 协作并非单向过程。你可以像给团队其他成员留言一样，在 PR 评论或代码行内评论中标记 Copilot。Copilot 会读取这些评论，并启动新会话来处理请求。由于结果并非确定性的，无法提供固定提示文本。你可以要求 Copilot：

- 在每个代码文件顶部添加简要说明文件用途的注释标头。
- 为 TypeScript 和 Astro 文件添加 TSDoc 文档注释。
- 创建 README，说明 Astro 应用结构。

1. 添加评论，请求修改生成的文档，并像标记其他用户一样标记 **@copilot**。可以使用上述想法，或提出其他希望在代码库中看到的文档改进建议。
2. 选择 **View Session**，观察 Copilot 的工作过程。留意 Copilot 如何启动新会话来进行更新。
3. 选择 **Back to pull request**，即可返回 PR。

    ![Back to pull request](../../../_images/ex7-back-to-pr.png)

4. Copilot 完成更改后，PR 中应会出现新的提交。
5. 选择 **Files changed** 选项卡审查更改。

可以继续迭代，直到满意为止。确认满意后，可以将 PR 从草稿转换为可审查状态，再合并到主分支。

![将 PR 转为可审查状态](../../../_images/ex7-ready-for-review.png)

## 审查相关游戏功能

返回 Copilot 为解决“在游戏详情页显示相关游戏”议题而创建的 PR。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 选择标题类似于 **Show related games on the game details page** 的 PR。
4. 选择 **Files changed** 选项卡，审查生成的代码。
5. 审查更新并确认没有问题后，返回 **Conversation** 选项卡并向下滚动。
6. 确认页面显示有工作流等待批准。
7. 如果工作流正在等待批准，请选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

8. PR 的检查部分应显示工作流已排队。如果一切正常，单个 Astro 应用的项目检查将通过。这可能需要几分钟。
9. **可选：** 可以在 Codespace 中切换到此分支，手动测试相关游戏功能。打开 Codespace 中的终端，并运行以下命令（将 `<branch-name>` 替换为 Copilot 创建的分支名称，例如 **copilot/fix-8**）：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

Copilot 已完成相关游戏功能！和之前一样，可以使用 Copilot 云智能体迭代并请求更新。例如，可以要求 Copilot 调整显示的相关游戏数量，或添加注释标头和 TSDoc 文档注释（请记住，在更新自定义说明**之前**就已分配此任务）。和之前一样，可以在 **Conversation** 选项卡中添加评论，Copilot 会读取评论并启动新会话。

## 审查无障碍功能

最后，审查使用无障碍自定义智能体实现的功能。此 PR 应同时包含练习 3 中分配的高对比度模式，以及练习 4 中通过控制中心提出的浅色模式。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 选择标题类似于 **Add high contrast mode to website** 的 PR。

> [!NOTE]
> 如果 Copilot 仍在处理任务，拉取请求会带有 **[WIP]** 标记。此时请等待 Copilot 完成工作，这可能需要几分钟。

4. 选择 **Files changed** 选项卡审查生成的代码。
5. 审查实现时，重点留意：
   - 用于切换模式的 UI 组件
   - 用于保存用户偏好的本地存储
   - 高对比度和浅色模式的 CSS 或样式更改
   - 无障碍属性（ARIA 标签、键盘导航等）
   - 管理模式切换的 JavaScript/TypeScript 代码

6. 审查更新并确认没有问题后，返回 **Conversation** 选项卡并向下滚动。
7. 确认页面显示有工作流等待批准。
8. 如果工作流正在等待批准，请选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. PR 的检查部分应显示工作流已排队。如果一切正常，单个 Astro 应用的项目检查将通过。这可能需要几分钟。
10. **可选：** 可以在 Codespace 中切换到此分支，手动测试无障碍功能。打开 Codespace 中的终端，并运行以下命令（将 `<branch-name>` 替换为 Copilot 创建的分支名称）：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    然后启动应用，在浏览器中测试高对比度和浅色模式切换，并确认页面重新加载后设置仍然有效。

留意无障碍自定义智能体如何引导 Copilot 按照无障碍最佳实践实现这些功能。如果发现无障碍方面的问题或改进空间，可以像处理之前的 PR 一样，在评论中标记 **@copilot** 并请求更新。

## 可选练习 — 继续委派任务

云智能体最适合处理真实待办事项，让你能把注意力转向其他工作。要养成这种习惯，可以在存储库中创建更多议题并分配给 Copilot。可以尝试：

- 在游戏详情页创建支持者意向表单。
- 为游戏列表页添加分页。
- 为数据访问辅助函数添加输入验证和错误处理。

## 总结

你已完成云智能体工作流。通过这些课程，你：

- **检查了存储库附带的自定义说明文件**，以便了解它们对云智能体输出的影响。
- **将议题分配给 Copilot 云智能体**，并观察它设置环境、制定计划和异步执行任务。
- **创建并使用自定义智能体**处理无障碍功能，添加高对比度和浅色模式切换。
- **使用 Copilot 智能体页面作为控制中心**，监视并引导正在进行的无障碍会话。
- **审查并迭代云智能体的拉取请求**，标记 `@copilot` 请求更改并批准工作流。

## 回顾和后续步骤

你已完成云智能体工作流。如果希望继续探索，其他工作流可以补充本次练习：

- 🖥️ **[VS Code 工作流](../../vscode/)** — 直接在 IDE 中体验 Copilot Chat 智能体模式和 MCP 集成。
- 💻 **[CLI 工作流](../../cli/)** — 在终端中交付两项经过审查的更改，体验 Plan 和 Autopilot 模式、智能体技能、自定义 QA 智能体、Playwright MCP、Agent Merge，以及可选的 `/delegate` 云智能体桥接。

你也可以在自己的存储库中尝试以下后续任务：

- 将重构或测试覆盖率议题分配给云智能体。
- 为其他领域创建自定义智能体。
- 为不同技术栈设置 `copilot-setup-steps.yml`。

## 资源

- [GitHub Copilot][github-copilot]
- [关于 Copilot 智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]
- [配置 Copilot 云智能体防火墙][agent-firewall]

---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
