---
title: "练习 1 - 自定义说明（云智能体）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：Copilot 云智能体 →][next-lesson]

使用生成式 AI 时，上下文至关重要。如果任务需要以特定方式完成，或者 Copilot 需要了解背景信息，就要确保它能够获取这些上下文。[说明文件][instruction-files]可以提供这些指导，让 Copilot 不仅了解你希望完成什么，还了解应该如何完成。

在本练习中，你将：

- 了解如何通过存储库自定义说明和按路径应用的说明文件，为 Copilot 提供项目上下文、编码准则和文档标准；
- 在 `.github/copilot-instructions.md` 中添加一项新的存储库级标准。

> [!NOTE]
> 与 VS Code 和 CLI 工作流不同，本练习不会使用“前后对比”提示。Copilot 云智能体会异步处理 GitHub 议题，因此难以实时并排展示效果。稍后在本工作流中审查云智能体创建的拉取请求时，可以看到说明文件带来的影响。

## 说明文件

### 场景

Tailspin Toys 作为一家优秀的软件开发公司，制定了一套开发实践准则和要求，包括：

- 数据层始终需要单元测试。
- UI 应采用深色模式，并呈现现代风格。
- 应通过 TSDoc 文档注释为代码添加文档。
- 每个文件开头都应添加一段注释，说明文件的用途。

使用说明文件，可以确保 Copilot 获取必要信息，并按照上述实践完成任务。

### 自定义说明

自定义说明可以向 Copilot 提供上下文和偏好设置，帮助它理解你的编码风格和要求。这项功能可以引导 Copilot 提供更相关的建议和代码片段。你可以指定偏好的编码约定、库，甚至喜欢采用的注释形式。说明既可以面向整个存储库，也可以针对特定文件类型或任务。

说明文件分为两类：

- `.github/copilot-instructions.md`：面向该存储库每个请求的单个说明文件。文件应包含项目级信息，即大多数 Copilot Chat 或 CLI 请求都适用的上下文，例如所用技术栈、项目概述、最佳实践和全局指导。
- `.github/instructions/*.instructions.md`：可针对特定任务或文件类型创建的说明文件。可以用它们为特定语言（例如 TypeScript）或任务（例如创建 UI 组件或单元测试）提供指导。

> [!NOTE]
> 在 IDE 中，说明文件只用于 Copilot Chat 的代码生成，不用于代码补全或下一处编辑建议。
>
> Copilot Chat、Copilot CLI 和 Copilot 云智能体在生成代码时都会使用存储库级说明，以及带有 `applyTo` frontmatter 的 `*.instructions.md` 文件。
>
> 此外，Copilot [也支持其他标准的说明文件][custom-instructions-support]，包括 AGENTS.md 和 CLAUDE.md 文件。

### 管理说明文件的最佳实践

完整讨论如何创建说明文件超出了本研讨会的范围。不过，示例项目提供了有代表性的做法。总体而言：

- 让 `copilot-instructions.md` 专注于项目级指导，例如项目内容、项目结构和全局编码标准。
- 使用 `*.instructions.md` 文件为特定文件类型（单元测试、Astro 组件、数据层）或特定任务提供指导。
- 使用自然语言，让指导清晰易懂，并提供代码示例说明什么样的代码合适或不合适。

创建说明文件没有唯一方法，使用 AI 也一样。通过尝试，找到适合项目的做法。

> [!TIP]
> 每个使用 GitHub Copilot 的项目都应具备完善的说明文件集合。查看此项目中的说明文件时，你可能会注意到其中涵盖了多种任务，例如 [UI 更新][ui-instructions]和 [Astro][astro-instructions]。
>
> Copilot 也可以帮助你生成说明文件。不同使用界面中的入口各不相同（例如 VS Code 中的 **Configure Chat → Generate Agent Instructions**，或 Copilot CLI 中的 `/init`）；当前工作流的课程会在相关位置介绍具体操作。
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
4. 打开 `.github/instructions/unit-tests.instructions.md`。注意文件开头的 `applyTo` 字段，它设置了相对于存储库根目录的 glob，用于确定适用文件。此处，任何 TypeScript 测试文件（例如匹配 `**/*.test.ts` 的文件）都符合条件。
5. 留意此项目中针对创建单元测试的说明。
6. 最后，打开 `.github/instructions/drizzle.instructions.md` 并滚动到末尾。注意其中指向其他说明文件（例如 `unit-tests.instructions.md`）和项目现有文件的链接。这样可以将较大的说明拆分为更小、可重复使用的文件，并向 Copilot 提供可参考的示例。（其中的路径相对于说明文件，而非存储库根目录。）

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分记录了项目编码标准，但尚未要求为代码添加文档。接下来的步骤会添加 TSDoc 文档注释和文件注释标头的规则。

## 添加新的存储库标准

本练习中唯一需要编辑的内容是：添加一项项目级规则，要求使用 TSDoc 文档注释和文件级注释标头。后续在本工作流中分配议题时，云智能体会遵循这项规则。

编辑前，先创建并切换到一个分支（云智能体会读取议题目标分支上的说明，但仍建议先整理好自己的提交）：

1. 在 codespace 终端中创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions-cloud
   ```

如前所述，`.github/copilot-instructions.md` 用于向 Copilot 提供项目级信息。现在添加代码文档标准，以改进代码建议。

1. 重新打开 `.github/copilot-instructions.md`。
2. 找到 **Code formatting requirements** 部分，该部分通常位于第 27 行附近。注意其中记录了项目编码标准，但尚无代码文档规则，因此生成的辅助函数没有文档注释。
3. 在现有标准下方添加以下 Markdown 行，要求 Copilot 添加文件注释标头和 TSDoc 文档注释：

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. 保存 `copilot-instructions.md`。

> [!TIP]
> 如上一课所述，可以在存储库级别（`.github/copilot-instructions.md`）创建适用于全局的说明，也可以创建针对特定语言、文件类型或任务的 `*.instructions.md` 文件。对于刚添加的文档注释规则等项目级标准，存储库级文件是合适的位置。

## 提交、推送并合并说明更新

云智能体会读取议题目标分支上的说明。下一练习将把议题分配给 Copilot，而 Copilot 会从 `main` 创建分支，因此必须先将说明更改合并到 `main`，云智能体才能使用这些规则。

1. 暂存并提交：

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. 推送分支：

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. 在 github.com 上创建从 `custom-instructions-cloud` 到 `main` 的拉取请求并合并。这样，云智能体在下一练习处理议题时就能读取这些说明。

> [!TIP]
> 如果你希望在本研讨会中直接使用 `main`，可以跳过分支步骤，直接提交到 `main`。此处采用分支，是为了模拟真实项目中的工作方式。

## 总结和后续步骤

你已了解 Copilot 如何读取此项目中的说明文件，并在 `.github/copilot-instructions.md` 中添加了新的存储库级标准。接下来可以在云智能体生成的拉取请求中看到这项标准的实际效果。

接下来，[将第一个议题分配给 Copilot 云智能体][next-lesson]。

## 资源

- [用于自定义 GitHub Copilot 的说明文件][instruction-files]
- [创建自定义说明的最佳实践][instructions-best-practices]
- [编写更好的 Copilot 自定义说明的 5 个技巧][copilot-instructions-five-tips]
- [Awesome Copilot — 说明文件和其他资源集合][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
