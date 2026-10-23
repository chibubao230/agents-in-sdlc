---
title: "练习 1 - 自定义说明（VS Code）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：智能体模式 →][next-lesson]

使用生成式 AI 时，上下文至关重要。如果任务需要以特定方式完成，或者 Copilot 需要了解背景信息，就要确保它能够获取这些上下文。可以通过多种方式向 Copilot 提供具体上下文，其中关键的一种是[说明文件][instruction-files]，可用于指导代码生成。

本练习将学习如何：

- 了解如何通过存储库自定义说明和按路径应用的说明文件，为 Copilot 提供项目上下文、编码准则和文档标准；
- 在当前说明生效的情况下，生成筛选功能的第一个数据切片（发行商辅助函数）；
- 在 `.github/copilot-instructions.md` 中添加一项新的存储库级标准；
- 重新运行相同的提示，观察生成的代码如何采用新标准；
- 将说明更新和筛选功能切片提交到 `main`，以便 Copilot 在下一练习中使用更新后的指导。

> [!CAUTION]
> 生成的代码可能不完全符合你设定的部分标准。Copilot 的结果并非确定性输出。本练习的重点是观察更新说明后行为变化的**趋势**，而不是要求输出逐字一致。

## 说明文件

### 场景

Tailspin Toys 制定了一套开发实践准则和要求，包括：

- 数据层始终需要单元测试。
- UI 应采用深色模式，并呈现现代风格。
- 应通过 TSDoc 文档注释为代码添加文档。
- 每个文件开头都应添加一段注释，说明文件的用途。

使用说明文件，可以确保 Copilot 获取必要信息，并按照这些实践完成任务。

### 自定义说明

自定义说明可以向 Copilot 提供上下文和偏好设置，帮助它理解你的编码风格和要求。你可以指定偏好的编码约定、库，甚至喜欢采用的注释形式。说明既可以面向整个存储库，也可以针对特定文件类型或任务。

说明文件分为两类：

- `.github/copilot-instructions.md`：面向该存储库每个请求的单个说明文件。文件应包含项目级信息，例如所用技术栈、项目概述、最佳实践和全局指导。
- `.github/instructions/*.instructions.md`：可针对特定任务或文件类型创建的说明文件。可以用它们为特定语言（例如 TypeScript）或任务（例如创建 UI 组件或单元测试）提供指导。

> [!NOTE]
> 在 IDE 中，说明文件只用于 Copilot Chat 的代码生成，不用于代码补全或下一处编辑建议。
>
> Copilot Chat、Copilot CLI 和 Copilot 云智能体在生成代码时都会使用存储库级说明，以及带有 `applyTo` frontmatter 的 `*.instructions.md` 文件。
>
> 此外，Copilot [也支持其他标准的说明文件][custom-instructions-support]，包括 AGENTS.md 和 CLAUDE.md 文件。

### 管理说明文件的最佳实践

完整讨论如何创建说明文件超出了本研讨会的范围。不过，示例项目提供了有代表性的做法。总体而言：

- 让 `copilot-instructions.md` 专注于项目级指导，例如项目内容、结构和全局编码标准。
- 使用 `*.instructions.md` 文件为特定文件类型（单元测试、Astro 组件、数据层）或特定任务提供指导。
- 使用自然语言，让指导清晰易懂，并提供代码示例说明什么样的代码合适或不合适。

创建说明文件没有唯一方法，使用 AI 也一样。通过尝试，找到适合项目的做法。

> [!TIP]
> 每个使用 GitHub Copilot 的项目都应具备完善的说明文件集合。查看此项目中的说明文件时，你可能会注意到其中涵盖了多种任务，例如 [UI 更新][ui-instructions]和 [Astro][astro-instructions]。
>
> Copilot 也可以帮助生成说明文件。不同使用界面的入口各不相同（例如 VS Code 中的 **Configure Chat → Generate Agent Instructions**，或 Copilot CLI 中的 `/init`）；当前工作流的课程会在相关位置介绍具体操作。
>
> 想寻找模板或起点？可以查看 [awesome-copilot][awesome-copilot]，其中汇集了说明文件、自定义智能体和其他资源。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## 浏览此项目中的自定义说明文件

花些时间阅读此存储库附带的说明文件：一个核心 `copilot-instructions.md` 文件，以及一组用于不同任务的 `*.instructions.md` 文件。可以在编辑器或 GitHub Web 界面中打开这些文件。

1. 打开 `.github/copilot-instructions.md`。
2. 浏览文件，留意项目简介以及 **Agent notes**、**Code standards**、**Scripts** 和 **Repository Structure** 等部分。在 **Code standards** 下，注意其中的嵌套 **GitHub Actions Workflows** 指导。这些内容适用于你与 Copilot 的任何交互。
3. 打开 `.github/instructions` 文件夹并浏览其中内容。注意其中包含 Astro 文件、Drizzle 数据层、测试等方面的说明。
4. 打开 `.github/instructions/unit-tests.instructions.md`。注意文件开头的 `applyTo` 字段，它设置了相对于存储库根目录的 glob，用于确定适用文件。例如，匹配 `**/*.test.ts` 的 TypeScript 测试文件符合条件。
5. 留意此项目中针对创建单元测试的说明。
6. 最后，打开 `.github/instructions/drizzle.instructions.md` 并滚动到末尾。注意其中指向其他说明文件（例如 `unit-tests.instructions.md`）和项目现有文件的链接。这让你能够将大型说明拆分为更小、可复用的文件，并向 Copilot 提供可参考的示例。（其中路径相对于说明文件，而非存储库根目录。）

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分记录了项目编码标准，但尚未要求为代码添加文档。接下来的步骤会添加 TSDoc 文档注释和文件注释标头规则。

## 为更改创建分支

遵循最佳实践，为更改创建一个分支。

1. 返回上一练习使用的 codespace。
2. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）打开新终端。
3. 创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions
   ```

## 更新说明前先使用 Copilot Chat

> [!TIP]
> **打开 Copilot Chat**
>
> 开始下面的练习前，返回 codespace，打开 Copilot Chat 面板并选择 **New Chat**，开始一个干净的对话。不同练习使用的模式和模型可能不同；各步骤会在相关位置说明。

为了观察自定义说明的影响，先在当前说明文件生效的情况下发送提示。之后更新说明，再发送相同提示并比较结果。

1. 关闭之前练习留下的编辑器标签页，让 Copilot 只获取所需上下文。
2. 打开 `src/lib/publishers.ts`，让 Copilot 了解辅助函数应放在哪里。
3. 在 Chat 视图的智能体下拉菜单中选择 **Agent**，使 Copilot 可以应用文件更改。

   ![Chat 视图中的智能体选择器截图](../../../_images/shared-chat-mode-selector.png)

4. 发送以下提示：

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot 会探索项目并应用代码更新，通常会涉及辅助函数文件和测试。
6. 注意建议的辅助函数是一个带类型的函数，它以 `db` 客户端作为第一个参数，并返回带类型的发行商数组。这来自 `.github/instructions/drizzle.instructions.md` 中适用于 `src/lib/*.ts` 的数据层约定。
7. 注意建议的代码**缺少** TSDoc 文档注释和文件级注释标头。

> [!CAUTION]
> Copilot 的输出具有概率性，因此它有时可能会在未收到明确指示的情况下添加文档注释。即使发生这种情况也没关系；重点是观察更新说明后行为一致性的改善。

## 添加新的存储库标准

如前所述，`.github/copilot-instructions.md` 用于向 Copilot 提供项目级信息。现在添加编码标准，以改进代码建议。

1. 重新打开 `.github/copilot-instructions.md`。
2. 找到 **Code formatting requirements** 部分，该部分通常位于第 27 行附近。留意其中记录的项目编码标准，但尚未涉及代码文档，因此生成的辅助函数没有文档注释。
3. 在现有标准下方添加以下 Markdown 行，要求 Copilot 添加文件注释标头和 TSDoc 文档注释：

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. 保存 `copilot-instructions.md`。

> [!TIP]
> 如上一课所述，可以在存储库级别（`.github/copilot-instructions.md`）创建适用于全局的说明，也可以创建针对特定语言、文件类型或任务的 `*.instructions.md` 文件。对于刚添加的文档注释规则等项目级标准，存储库级文件是合适的位置。

## 重新运行提示并观察更改

1. 返回 Copilot Chat 并选择 **New Chat**，清除上下文。
2. 再次打开 `src/lib/publishers.ts`，让 Copilot 专注于正确的文件。
3. 发送与之前**完全相同的提示**：

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. 留意建议的文件现在以类似以下注释块开头：

   ```typescript
   /**
    * Publisher data-access helpers for the Tailspin Toys Crowd Funding platform.
    * Provides functions to retrieve publisher information from the database.
    */
   ```

5. 留意建议的函数现在包含类似以下的 TSDoc 注释：

   ```typescript
   /**
    * Returns a list of all publishers with their id and name.
    *
    * @param db - The Drizzle database client.
    * @returns A promise that resolves to an array of publisher objects.
    */
   ```

你刚刚引导 Copilot 遵循新的项目标准，并将其应用到下一练习会继续使用的真实代码中。

## 提交说明并推送分支

说明文件和存储库中的其他资源一样，都应使用相同的源代码管理方式。现在将更改提交并推送到存储库。

1. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）打开新终端。
2. 在终端中运行以下命令，确认说明更新和辅助函数更改都已存在：

   ```bash
   git status
   ```

3. 在终端中暂存并提交说明更新，以及 Copilot 为辅助函数基础所做的所有更改：

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # 如果 git status 显示了其他支持性更改（例如测试），也请将这些文件加入暂存区。
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. 在终端中将分支推送到存储库：

   ```bash
   git push -u origin custom-instructions
   ```

## 创建并合并拉取请求

分支推送后，创建拉取请求，并将其关联到待办事项中的文档标准议题。也可以让 Copilot 使用已连接到项目的 GitHub 工具代为完成：提示它查找议题、创建可关闭该议题的 PR，然后合并。

> [!NOTE]
> 这是第一次让 Copilot 代表你操作 GitHub。项目模板已将 GitHub 工具连接到工作区，因此无需额外设置。Copilot 首次使用工具时，VS Code 可能会提示你登录 GitHub。按提示完成授权。后续练习会介绍此连接的工作方式（Model Context Protocol）。

1. 在 codespace 中打开 Copilot Chat。
2. 按 <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd>（Windows/Linux）打开 Copilot Chat 视图，并确认智能体选择器中已选择 **Agent**。
3. 让 Copilot 查找与更新说明文件相关的议题，并从当前分支创建 PR，说明说明更新和新的发行商辅助函数基础：

   ```
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Copilot 会开始查找议题并创建 PR。
5. Copilot 提示你允许其代为执行 GitHub 操作时，请审查命令并酌情选择 **Allow**。
6. PR 创建后，使用以下提示让 Copilot 合并 PR 并将本地分支切换回 main：

   ```
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

7. Copilot 提示你允许其代为执行 GitHub 操作和 shell 命令时，请审查命令并酌情选择 **Allow**。

现在，你已在 GitHub Copilot 的帮助下创建并合并了拉取请求！

## 总结和后续步骤

你已了解 Copilot 如何读取此项目中的说明文件，然后在 VS Code 中使用 Copilot Chat 来：

- 发送代码生成提示，并观察 Copilot 在**现有**说明下生成的内容；
- 在 `.github/copilot-instructions.md` 中添加新的存储库级标准；
- 重新发送相同提示，观察建议的代码如何采用新标准；
- 将说明更新和辅助函数基础提交到 `main`，以便下一练习可以继续使用。

接下来，在[智能体模式][next-lesson]中使用这些说明，让 Copilot 跨代码库添加新功能。

## 资源

- [用于自定义 GitHub Copilot 的说明文件][instruction-files]
- [创建自定义说明的最佳实践][instructions-best-practices]
- [编写更好的 Copilot 自定义说明的 5 个技巧][copilot-instructions-five-tips]
- [GitHub Copilot 个人自定义说明][personal-instructions]
- [Awesome Copilot — 说明文件和其他资源集合][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
