---
title: "练习 2 - GitHub Copilot 云智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义说明][previous-lesson] |
|:--|

几乎所有组织都面临技术债务，完全没有这类问题的组织少之又少。技术债务可能是尚未解决的安全问题、需要更新的旧代码，或因缺少时间而长期搁置的功能请求。GitHub Copilot 云智能体可以自主执行更新代码、添加功能等任务。完成后，它会创建一个草稿 PR，供开发人员审查。这样可以将繁琐任务交给智能体，加快开发流程，让开发人员专注于更重要的工作。

本练习将探索 Copilot 云智能体如何：

- 自定义代码生成环境。
- 确保操作安全执行。
- 处理范围明确的议题。
- 接收分配的议题。

## 场景

Tailspin Toys 希望解决一些技术债务。最初受雇开发网站的承包商没有妥善处理文档——实际上，你会发现文档完全缺失。首先，他们希望为应用中所有导出的函数添加 TSDoc 文档注释。

此外，设计团队希望改进游戏发现体验。他们希望每款游戏的详情页显示相关游戏，即同一类别中的其他游戏，让用户可以继续浏览。他们目前不需要完善的实现，只希望能用于 UX 验收测试。这项工作目前受到阻碍，但还有其他优先级更高的议题。

这些任务很容易被降级处理，因此很适合分配给 Copilot 云智能体。云智能体可以异步处理任务，让开发人员先做其他工作，之后再回来审查 Copilot 的成果并确认符合预期。

## 了解 GitHub Copilot 云智能体

[GitHub Copilot 云智能体](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent)可以像开发人员一样在后台执行任务。与人类开发人员协作类似，你可以通过多种方式分配工作，其中一种是[将 GitHub 议题分配给 Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)。分配后，Copilot 会创建草稿拉取请求来跟踪进度、设置环境并开始处理任务。你可以在会话进行中或完成后查看 Copilot 的会话。方案准备好供你审查时，Copilot 会在拉取请求中标记你。

## 编写范围明确的说明

GitHub Copilot 并没有什么魔法——你仍然需要思考。即便看起来简单的操作，仔细分析后也会发现其中的复杂性，因此要[留意如何为 Copilot 云智能体界定任务范围][cloud-agent-best-practices]。把它当作 AI 结对编程伙伴：分阶段开展工作，在过程中学习、尝试并调整。加入生成式 AI 并不会改变软件开发的基本原则。

## 此存储库中的自定义说明

前面的练习介绍了自定义说明及其如何引导 Copilot。如果你已经完成自定义说明练习，就见过 `.github/copilot-instructions.md` 的作用；否则，现在可以快速查看。在将工作分配给 Copilot 云智能体之前，以只读方式查看存储库中已有的说明文件，之后就能留意它们产生的影响。

在存储库的 GitHub Web 界面中，或在已启动的 codespace 中打开以下文件：

- `.github/copilot-instructions.md`：查看 **Code standards** 部分，特别留意 TypeScript 约定和 TSDoc 文档注释的要求。
- `.github/instructions/unit-tests.instructions.md`：留意 `applyTo` frontmatter，它将这些说明应用于 `**/*.test.ts` 文件。

下一部分将把 `Code lacks documentation` 议题分配给云智能体。观察生成的拉取请求中是否包含 TSDoc 文档注释、TypeScript 约定和注释标头。这些要求来自说明文件，之后的练习审查 PR 时还会再次说明。

## 为 Copilot 云智能体设置开发环境

编写代码通常需要特定环境并运行一些设置脚本，以确保环境状态正常；无论由谁编写代码都如此。将任务分配给 Copilot 时也一样，因为它会像软件工程师一样执行工作。

云智能体使用 [GitHub Actions][github-actions] 作为工作环境。你可以创建[专用设置工作流][setup-workflow]来定制环境，并将其配置在 `.github/workflows/copilot-setup-steps.yml` 文件中，使其在智能体开始工作前运行。这能让智能体访问所需的开发工具和依赖项。实验室已预先配置此环境，以便顺利完成练习并提供学习机会。配置确保 Copilot 可以访问 Node.js、项目依赖项、端到端测试所需的浏览器二进制文件，以及已迁移并填充数据的单个 Astro 应用 SQLite 数据库：

```yaml
name: "Copilot Setup Steps"

# 允许在存储库的 "Actions" 选项卡中测试设置步骤
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # 仅授予步骤所需的最低权限。Copilot 会使用自己的令牌执行操作。
    permissions:
      # 如果设置步骤需要克隆存储库（例如安装依赖项），则需要 contents: read 权限。如果不在设置步骤中克隆存储库，Copilot 会在步骤完成后自动克隆。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # 前端 / 应用设置 - Node.js（整个应用现已使用 Astro + Drizzle/libSQL）
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # 迁移并填充本地 SQLite 数据库，以便构建和测试可以使用数据。
      - name: Set up the database
        run: npm run db:setup
```

该文件看起来和其他 GitHub 工作流类似，但有几个关键点：

- 它包含一个名为 `copilot-setup-steps` 的作业。此作业会在 Copilot 开始处理拉取请求前于 GitHub Actions 中运行。
- 留意 `workflow_dispatch` 触发器，它允许你在存储库的 **Actions** 选项卡中手动运行工作流。这样可以在等待 Copilot 运行之前，先验证工作流是否正常。

## 添加文档

大家都明白文档的重要性，但大多数项目的文档要么过时，要么完全缺失。这类技术债务常常无人处理，会降低工作效率，让代码库更难维护，也更难让新开发人员加入团队。Copilot 擅长编写文档，因此很适合将这项任务分配给云智能体。它会在后台生成所需文档。后续练习中，你将回来审查其成果。

1. 在新的浏览器标签页中打开 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue** 创建新议题。
5. 将 **Title** 设置为 `Code lacks documentation`。
6. 将 **Description** 设置为：

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

  ![将 Copilot 分配给议题](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。

  ![Copilot 分配详情](../../../_images/ex4-assign-copilot-details.png)

10. 选择 **Pull Requests** 选项卡。
11. 打开新生成的拉取请求（PR），其标题类似于 `[WIP]: Code lacks documentation`。如果列表中尚未显示新 PR，稍等片刻并刷新浏览器。
12. 几分钟后，Copilot 应会在 PR 中创建待办事项列表。

> [!NOTE]
> Copilot 的待办事项列表可能需要几分钟才会出现在 PR 中。Copilot 正在创建环境（运行前面介绍的工作流）、分析项目并确定解决问题的方案。

13. 检查列表及其将执行的任务。
14. 向下滚动查看拉取请求时间线，确认 Copilot 已开始处理议题。
15. 选择 **View session** 按钮。

  ![Copilot 会话视图](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 可能需要刷新窗口才能看到更新后的指示器。

16. 浏览实时会话，了解 Copilot 如何解决问题，包括探索代码并了解当前状态、停下来思考和确定合适方案，以及创建代码。

这很可能需要几分钟。Copilot 云智能体的主要目标之一是异步执行任务，让我们可以专注于其他工作。现在利用这一特性，再向云智能体分配一项任务，然后把注意力转向为应用编写新功能。

## 在游戏详情页添加相关游戏区域

如前所述，GitHub Copilot 云智能体的一大优势是可以分工，让你专注于一组任务，而智能体处理另一组任务。为设计团队添加相关游戏区域可能不需要很久，但这段时间仍可用于其他工作。现在就把任务分配给 Copilot 云智能体。

1. 返回 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue** 使用空白模板。
5. 将 **Title** 设置为 `Show related games on the game details page`。
6. 将 **Description** 设置为：

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

  ![将 Copilot 分配给议题](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。

不久后，你会在议题的第一条评论中看到一组 👀，表示 Copilot 已开始处理。

![Copilot 使用眼睛表情符号表示正在处理议题](../../../_images/ex4-issue-eyes-emoji.png)

Copilot 正在认真处理第二项请求！云智能体的工作方式类似软件工程师，因此无需一直监视它，等工作完成后再审查即可。接下来创建并使用自定义智能体。

## 总结和后续步骤

本课介绍了 [GitHub Copilot 云智能体][copilot-agents]。你可以将议题分配给云智能体，让它异步处理。可以使用 Copilot 解决技术债务、创建新功能，或协助将代码从一个框架迁移到另一个框架。

本课探索了以下概念：

- 自定义代码生成环境。
- 确保操作安全执行。
- 了解范围明确的议题的重要性。
- 将议题分配给 Copilot。

云智能体正在后台工作，现在可以开始创建并使用自定义智能体。[Copilot 云智能体也支持 MCP 服务器][cloud-agent-mcp]，并可以使用前面模块介绍的自定义说明。

## 资源

- [关于 Copilot 云智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]

| [下一课：自定义智能体 →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
