---
title: "第 4 课 - 使用自定义说明引导 Copilot"
description: "了解存储库说明，添加文档标准，并将其应用于筛选代码。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

使用生成式 AI 时，上下文至关重要。如果任务需要以特定方式完成，就要确保 Copilot 可以获取相应指导。[说明文件][instruction-files]不仅说明你希望代码实现什么，还说明代码应如何组织。现在你已构建筛选功能，接下来将了解 Copilot 使用的说明、添加文档标准并将其应用到代码中。

本课将学习如何：

- 了解存储库说明和按路径应用的说明文件如何传递给智能体。
- 更新说明文件，确保遵循编码标准。
- 了解说明文件对代码的影响。

## 场景

Tailspin Toys 制定了一套开发实践准则和要求，包括：

- 注释应解释意图和不明显的决策，而不是重复代码内容。
- `db/` 和 `src/lib/` 中导出的函数应使用 TSDoc/JSDoc 记录用途、参数和返回值；如果函数有可注入的 `db` 参数，也应说明。
- 可复用的 Astro 组件应记录其 `Props` 契约；相关代码更改时，注释也应保持最新。
- 应保留现有格式和 lint 指导。

使用说明文件，可以确保 Copilot 获取必要信息，并按照上述实践完成任务。

## 说明文件

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

## 探索此项目中的自定义说明文件

花些时间阅读此存储库附带的说明文件：一个核心 `copilot-instructions.md` 文件，以及一组用于不同任务的 `*.instructions.md` 文件。可以在编辑器或 GitHub Web 界面中打开这些文件。

1. 打开 `.github/copilot-instructions.md`。
2. 浏览文件，留意项目简介以及 **Agent notes**、**Code standards**、**Scripts** 和 **Repository Structure** 等部分。在 **Code standards** 下，注意其中的嵌套 **GitHub Actions Workflows** 指导。这些内容适用于你与 Copilot 的任何交互。
3. 打开 `.github/instructions` 文件夹并浏览其中内容。注意其中包含 Astro 文件、Drizzle 数据层、测试等方面的说明。
4. 打开 `.github/instructions/unit-tests.instructions.md`。注意文件开头的 `applyTo` 字段，它设置了相对于存储库根目录的 glob，用于确定适用文件。例如，匹配 `**/*.test.ts` 的 TypeScript 测试文件符合条件。
5. 留意此项目中针对创建单元测试的说明。
6. 最后，打开 `.github/instructions/drizzle.instructions.md` 并滚动到末尾。注意其中指向其他说明文件（例如 `unit-tests.instructions.md`）和项目现有文件的链接。这让你能够将大型说明拆分为更小、可复用的文件，并向 Copilot 提供可参考的示例。（其中路径相对于说明文件，而非存储库根目录。）

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分记录了项目编码标准，但尚未要求为代码添加文档。接下来的步骤会添加 TSDoc 文档注释和文件注释标头规则。

## 更新说明文件以符合团队指导

现有文件虽然是不错的起点，但仍有不足。现在修改核心 `copilot-instructions.md` 文件，确保新生成的 TypeScript 代码包含 TSDoc 注释。

1. 在 `.github/copilot-instructions.md` 中找到 **Code formatting guidance** 部分，该部分通常位于第 35 行附近。
2. 在该部分末尾添加以下项目：

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

文件会自动保存！

## 使用更新后的指导

Copilot CLI 会在对话开始时加载存储库说明。保存说明更新后，退出并恢复筛选对话，以便 Copilot 在保留功能上下文的同时加载新指导。

1. 如果筛选对话仍处于打开状态，输入 `/exit` 关闭它。Copilot CLI 会自动保存对话。
2. 在存储库根目录的 `game-filters-cli` 分支上恢复已保存的对话：

   ```bash
   copilot --resume
   ```

   如果系统提示，请选择筛选对话，并确认已检出 `game-filters-cli` 分支。
3. 让 Copilot 应用更新后的指导：

   ```plaintext
   We just updated our instructions and code guidance. Can you please update the code you generated to match that guidance?
   ```

4. 输入 `/diff` 并查看已更改的 TypeScript 文件。留意新生成的 TSDoc 注释，并确认它们准确说明了代码。

## 总结和后续步骤

你已了解 Copilot 如何读取说明文件，并将新标准应用于功能。本课中，你：

- 浏览了存储库说明和按路径应用的说明文件如何传递给智能体。
- 更新了说明文件，以确保遵循编码标准。
- 了解了说明文件对代码的影响。

接下来，[自定义并运行可复用的 quality-checks 技能][next-lesson]，确保一致地运行 lint 和测试。

## 资源

- [为 Copilot CLI 添加自定义说明][instruction-files]
- [自定义说明支持][custom-instructions-support]
- [创建自定义说明的最佳实践][instructions-best-practices]
- [Awesome Copilot — 说明文件和其他资源集合][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
