---
title: "练习 4 - 监视和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

在前几项练习中，你让 Copilot 云智能体处理了三个旨在改善用户体验和添加功能的独立任务。云智能体可以异步自主工作，但监视这些任务仍然很重要。

你可以使用多种工具管理分配给云智能体的任务，包括 GitHub.com 上的[智能体页面][agents-page]。在这个控制中心，你可以查看所有有未关闭拉取请求（PR）的智能体任务，了解它们执行的操作，还可以引导正在进行的会话。

本课将学习如何：

- 浏览智能体页面，监视云智能体任务。
- 引导正在进行的会话，请求添加更多功能。

## 场景

将创建高对比度模式的任务分配给智能体后，团队认为也适合添加浅色模式。既然已经在更新网站样式并添加切换功能，顺便加入这项功能也很合理。现在需要引导智能体，确保它同时添加浅色模式和高对比度模式。

## 查看 Copilot 云智能体任务

先查看分配给 Copilot 云智能体的所有任务当前状态。

1. 打开 [Copilot 智能体页面](https://github.com/copilot/agents)。
2. 留意主窗格和左侧窗格中的任务列表。应能看到已分配给 Copilot 的任务，例如：
    - 更新代码文档。
    - 生成用于修改游戏的 API。
    - 为网站添加高对比度模式。
3. 选择一个正在运行的任务，查看 Copilot 已执行的操作。可能包括：
    - 检出存储库代码。
    - 创建 Copilot 的工作环境。
    - 设置 MCP 服务器。
    - 执行完成指定任务所需的各种步骤。

> [!NOTE]
> 显示的具体步骤会因 Copilot 的工作状态和采用的方案而异。

4. 同时留意右侧显示的拉取请求（PR）窗格。你可以在此查看 PR 和已更改的文件，以便进一步监视。

## 引导云智能体

现在已经查看了活动任务，可以在 Copilot 实现高对比度模式的同时，让它加入浅色模式切换功能。

1. 选择与添加高对比度模式相关的会话。具体标题会因 Copilot 使用的名称和当前工作状态而异。

    ![控制中心中的无障碍会话](../../../_images/ex6-accessibility-session.png)

2. 观察会话几分钟，直到它显示设置已完成并开始处理任务。此时会看到类似下方的消息。
3. 在 **Steer active session while Copilot is working** 对话框中添加以下提示：

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![智能体页面中突出显示 Steer active session while Copilot is working 对话框的云智能体任务截图](../../../_images/ex6-steer-cloud-agent-task.png)

4. 按 <kbd>Enter</kbd> 发送提示。
5. 留意 Copilot 如何确认收到提示并将其纳入工作流程。

## 让 Copilot 继续工作

和之前一样，Copilot 会继续处理更新后的任务！它会先完成收到消息时正在执行的步骤，然后将新请求纳入后续工作。

与之前一样，这需要几分钟。你可以趁此暂停一下，回顾目前为止的学习和探索。

## 总结和后续步骤

本课介绍了 Copilot 智能体页面，它是监视和引导 GitHub Copilot 云智能体任务的中心。你可以在此跟踪所有活动和已完成的任务、审查正在执行的工作，还可以重定向进行中的任务以调整范围或提供额外指导。

本课探索了以下概念：

- 使用控制中心和智能体页面监视云智能体任务。
- 重定向正在进行的会话，请求添加更多功能。

Copilot 正在完成无障碍功能。接下来，[迭代 Copilot 创建的拉取请求][next-lesson]。

## 资源

- [Copilot 智能体页面][agents-page]
- [关于自定义智能体][custom-agents]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
