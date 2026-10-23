---
title: "练习 2 - 使用 Copilot 智能体模式添加功能"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

即使是最简单的应用更新，通常也需要修改多个文件并执行操作，例如运行测试。开发人员通常需要查找所有相关文件、进行更改、运行测试、调试、找出遗漏的文件，再次更新……诸如此类。

Copilot 智能体模式正适用于此类工作。

Copilot 智能体模式可以在 IDE 中更自主地工作。它的行为类似开发人员：先探索现有项目结构，再进行所需更新、运行测试等任务，并自动修复发现的问题。现在来看看如何使用智能体模式为网站添加新功能。

> [!NOTE]
> 智能体模式和云智能体的名称相似，但它们面向不同的使用场景。智能体模式在 IDE 中执行任务，可快速反馈并与用户交互。云智能体则像团队成员一样异步工作，通过议题和拉取请求与你协作。

本练习将学习如何：

- 使用 Copilot 智能体模式探索项目、查找相关文件并协调执行更改。
- 使用 GitHub Copilot 智能体模式跨 UI 和数据层实现新功能。
- 在合并到代码库前审查 Copilot 智能体模式生成的更改和测试。

## 场景

随着游戏列表不断增加，你希望用户可以按类别和发行商筛选。上一练习中已添加发行商辅助函数，现在使用 Copilot 智能体模式完成剩余的数据层、UI 和测试工作。

## 运行 Tailspin Toys 网站

进行任何更改前，先浏览 Tailspin Toys 网站，了解其当前功能。

该网站是一个采用开发者主题的桌游众筹平台。用户可以浏览游戏列表并查看详情。网站是一个单体 Astro 应用，页面会在构建时渲染为静态 HTML。页面通过 Drizzle ORM 在 frontmatter 中直接查询本地 SQLite 数据库——没有单独的后端 API，也没有客户端 UI 框架。可复用的数据访问辅助函数位于 `src/lib/`，交互功能则使用小型、作用范围明确的 Astro `<script>` 和标准 DOM API 实现。

### 启动网站

项目提供了 `npm run dev` 任务，用于启动单个 Astro 开发服务器。请按以下步骤在 GitHub Codespace 中运行：

1. 返回 codespace。上一练习结束后，你应该已切回 `main`。
2. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux），在 Codespace 中打开新终端。
3. 更新分支并为筛选工作创建新分支：

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 运行以下命令启动网站：

   ```bash
   npm run dev
   ```

   Astro 开发服务器就绪后，会显示类似以下 URL 的提示：

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> 如果出现提示，询问是否要为 `http://localhost:4321` 打开浏览器窗口，请选择 **x** 关闭。

5. 在终端中按住 <kbd>Command</kbd>（Mac）或 <kbd>Ctrl</kbd>（Windows/Linux），然后选择 Astro 服务器地址 `http://localhost:4321`，打开网站。

> [!NOTE]
> 使用 codespace 时，在 Codespace 终端中选择 localhost URL 会自动转到 `https://<your-codespace-name>-4321.app.github.dev/`。这是通往 codespace 的私有隧道，Web 服务器现已在其中运行！

### 浏览网站

网站运行后，可以浏览其功能。主要功能包括：

- **首页**：显示桌游列表，包括标题、图片和说明。
- **游戏详情页**：选择游戏后会打开详情页，显示游戏标题、说明、发行商和类别等信息。

## 使用 Copilot 探索待办事项

> [!TIP]
> **打开 Copilot Chat**
>
> 开始下面的练习前，返回 codespace，打开 Copilot Chat 面板并选择 **New Chat**，开始一个干净的对话。不同练习使用的模式和模型可能不同；各步骤会在相关位置说明。

网站的初始实现可以正常工作，但还可以添加新功能。先查看待办事项。使用模板设置项目时，系统已自动创建一组待办议题；现在让 GitHub Copilot 显示这些事项。

1. 在 Chat 视图的智能体下拉菜单中选择 **Agent**。**Agent** 智能体可以自主规划并跨文件实现更改、运行终端命令和调用工具。

   ![Chat 视图中的智能体选择器截图](../../../_images/shared-chat-mode-selector.png)

2. 从可用模型列表中选择 **Claude Sonnet 4.5**。

> [!CAUTION]
> 本研讨会作者并不偏好某一个模型。编写本研讨会时使用的是 Claude Sonnet 4.5，因此说明中采用该模型，希望获得相对一致的代码建议，提供良好体验。不过，由于大型语言模型具有概率性，你收到的建议可能与本研讨会描述不同。这完全正常，符合预期。

> [!NOTE]
> 由于大型语言模型具有概率性，Copilot 可能会使用不同的 MCP 命令，但仍应能完成任务。

3. 向 Copilot 发送以下提示，询问待办议题：

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. 选择 **Continue**，运行列出所有议题的命令。
5. 审查生成的议题列表。

注意，Copilot 还根据它认为对用户最有用的程度为事项安排了优先级。

## 审查说明文件

让智能体开始生成代码之前，先查看将用于向 Copilot 提供工作上下文的说明文件。这里将使用 [UI 用户界面（UI）说明文件](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md)，其中介绍了如何为网站添加功能。

1. 在 codespace 中打开 `.github/instructions/ui.instructions.md`。
2. 留意文件中的整体指导，其中包括：
   - 架构概述。
   - 组件设计、可测试性和无障碍原则。
   - 指向特定文件类型说明的链接，包括：
     - Astro
     - Tailwind CSS
     - Drizzle

> [!TIP]
> 说明文件可以引用其他说明文件和项目文件。路径相对于说明文件所在位置。这样可以复用内容，将复杂说明拆分为更易管理的部分，并提供示例和模板。

## 实现筛选功能

完成筛选功能至少需要更新应用中的三个部分：

- 在数据层（`src/lib/`）添加或完善剩余筛选逻辑。
- 添加或更新筛选行为测试。
- 更新游戏列表页，添加筛选 UI。

此外，合并前必须运行测试并确保通过。Copilot 智能体模式可以代你完成这些任务。现在开始添加功能。

1. 可以继续当前 Copilot 对话，也可以选择 **New Chat** 开始新对话。
2. 选择 **Add Context**、**Instructions** 和 **ui** 作为说明文件。

   ![选择 UI 说明文件的示例截图](../../../_images/ex3-select-instructions-file.png)

3. 确认 Chat 视图的智能体下拉菜单中仍选择 **Agent**。

   ![Chat 视图中的智能体选择器截图](../../../_images/shared-chat-mode-selector.png)

4. 确认仍选择 **Claude Sonnet 4.5** 作为模型。
5. 使用以下提示，让 Copilot 根据待办事项中的相关议题实现功能：

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. 观察 Copilot 如何先探索项目并查找相关文件。它应会定位数据层辅助函数、UI 和测试，然后修改文件并运行测试。

   ![Copilot 探索项目文件的截图](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot 会执行多项任务，例如探索项目、修改文件和运行测试。根据任务复杂度和代码库规模，这可能需要几分钟。在此期间，代码编辑器中可能会出现 **Keep** 和 **Undo** 按钮。Copilot 完成后，每项更改都会显示 **Keep** 或 **Undo**，因此无需在工作进行时选择。

7. Copilot 提示后，选择 **Continue** 运行测试。

   ![Copilot Chat 窗格中提示用户确认运行测试的对话框截图](../../../_images/ex3-agent-mode-run-tests.png)

8. 过程中可能会出现暂停，甚至有测试失败。这没关系！Copilot 会在代码生成和测试之间迭代，直到完成任务且未发现错误。

   ![Copilot 智能体模式完整 Chat 会话截图](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 检查生成的代码，查找潜在问题。

> [!CAUTION]
> 请记住，审查 Copilot 或其他 AI 工具生成的代码始终很重要。

10. 返回正在运行网站的浏览器，探索新功能。
11. 确认功能正常并审查代码后，在 Copilot Chat 窗口中选择 **Keep**。

## 总结和后续步骤

恭喜！本练习中，你了解了如何使用 GitHub Copilot 智能体模式为 Tailspin Toys 网站添加功能。你学会了：

- 使用 GitHub Copilot 智能体模式跨 UI 和数据层实现新功能。
- 使用 Copilot 智能体模式探索项目、查找相关文件并协调执行更改。
- 在合并到代码库前审查 Copilot 智能体模式生成的更改和测试。

接下来，[使用 Playwright MCP 服务器测试功能][next-lesson]，并为其创建拉取请求。

### 奖励探索练习 – 实现分页

随着游戏列表不断增加，需要添加分页。运用本练习学到的技能，提示 Copilot 更新网站以实现分页。可以考虑：

- 遵循现有最佳实践，包括使用现有说明文件。
- 思考分页的实现方式，例如是否允许用户选择每页大小，还是使用固定值。
- 编写提示时，提供 Copilot 所需的指导，以便按照预期实现功能。
- 可能需要通过迭代向 GitHub Copilot 请求更改并提供上下文。这是使用 Copilot 的正常工作流程！

## 资源

- [Copilot 的 ask、edit 和 agent 模式：用途和适用场景][choose-mode]
- [VS Code 中的智能体模式][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
