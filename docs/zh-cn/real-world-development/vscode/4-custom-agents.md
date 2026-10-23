---
title: "练习 4 - 自定义智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

## 什么是自定义智能体？

GitHub Copilot 中的[自定义智能体][custom-agents-concept]可以针对开发工作流中的特定任务或领域，创建专用 AI 助理。你可以在存储库的 `.github/agents` 文件夹中通过 Markdown 文件定义智能体，为 Copilot 提供专注的说明、最佳实践、编码模式和领域知识，从而引导它更有效地完成特定类型的工作。团队可以将专业知识编码为可复用的智能体，例如确保符合 [WCAG][wcag] 的无障碍智能体、遵循安全编码实践的安全智能体，或维护一致测试模式的测试智能体。

自定义智能体通过项目 `.github/agents` 文件夹中的 Markdown 文件定义，也可以在全局路径 `~/.copilot/agents` 中定义。每个文件至少包含 `name` 和 `description` YAML frontmatter，后跟定义智能体行为、专业知识和说明的 Markdown 提示。

### 自定义智能体与智能体技能的区别

自定义智能体和[智能体技能][agent-skills-concept]在概念上有些重叠。两者主要通过 Markdown 文件定义，并告诉 AI 如何执行操作。最简单的区分方式是：**自定义智能体**是工作者，**技能**是工具。

自定义智能体有自己的上下文窗口，旨在协调技能（甚至其他智能体）来完成工作。在本实验中，无障碍自定义智能体会依据无障碍指南审查并更新网站；执行任务时，它可以调用拉取请求工作流技能或运行并管理测试的技能。

> [!NOTE]
> 编写自定义智能体没有唯一的正确方式。与其他 AI 用法一样，应通过测试和迭代，找到适合自身环境和场景的做法。

[custom-agents-concept]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[agent-skills-concept]: https://docs.github.com/copilot/concepts/agents/about-agent-skills
[wcag]: https://www.w3.org/WAI/standards-guidelines/wcag/

## 场景

Tailspin Toys 致力于确保所有用户都能访问其众筹平台，不受视觉能力或偏好的影响。近期用户反馈指出，由于文字与背景颜色对比度不足，有些用户难以阅读当前的深色主题。为解决这一无障碍问题，设计团队要求实现一种可由用户开关的高对比度模式。

无障碍至关重要，因此你希望尽快实现此功能。接下来使用自定义智能体生成所需功能。

本练习将学习如何：

- 检查现有的无障碍自定义智能体。
- 在 Copilot Chat 中使用无障碍智能体实现高对比度模式。

## 检查无障碍自定义智能体

我们已为你创建了一个无障碍自定义智能体。先检查其内容，了解它将如何引导 Copilot。

返回 codespace，然后打开终端，从 `main` 创建一个新的无障碍功能分支（这样可以让练习 3 中的筛选 PR 保持独立）：

```bash
git checkout main
git pull
git checkout -b accessibility-vscode
```

1. 打开 `.github/agents/accessibility.md`。
2. 留意包含 `name` 和 `description` 字段的 YAML frontmatter。

> [!CAUTION]
> `name` 和 `description` frontmatter 是自定义智能体的必填项。

3. 扫读后续部分，了解其中的：
   - 生成无障碍网站代码时的核心职责。
   - 无障碍最佳实践。
   - HTML、CSS 和 JavaScript 代码示例。
   - 常见问题和错误列表。

## 在 Copilot Chat 中使用自定义智能体

VS Code 会在 Copilot Chat 视图底部的智能体下拉菜单中显示 `.github/agents` 中定义的所有自定义智能体。选择自定义智能体后，即可将聊天会话限定为使用该智能体的说明和工具。

> [!TIP]
> **打开 Copilot Chat**
>
> 开始下面的练习前，返回 codespace，打开 Copilot Chat 面板并选择 **New Chat**，开始一个干净的对话。不同练习使用的模式和模型可能不同；各步骤会在相关位置说明。

1. 如果 Chat 视图智能体下拉菜单中尚未选择 **Agent**，请先选择它。

   ![Chat 视图中的智能体选择器截图](../../../_images/shared-chat-mode-selector.png)

2. 选择 Chat 视图底部的智能体下拉菜单（其中会显示当前智能体，默认值为 **default**）。
3. 从可用智能体列表中选择 **Accessibility agent**。
4. 向无障碍智能体发送以下提示：

    ```plaintext
    Add a high-contrast mode to the site. There should be a toggle for high contrast which the user can set, and the setting should persist across page reloads using local storage on the browser.
    ```

5. Copilot Chat 会开始工作：探索代码库、提出更改建议并编辑项目文件。每项编辑都会在聊天中以内联形式显示文件路径和差异，供你审查。
6. 智能体修改文件时，**Files changed** 指示器会更新，显示它修改的工作文件集。

> [!NOTE]
> 此过程可能需要几分钟。Copilot 正在对存储库进行实际更改，可能会编辑 Astro 组件、CSS 和相关测试等现有文件。

下一练习中，你将审查并引导这项进行中的工作。

## 总结和后续步骤

本课介绍了 GitHub Copilot 中的[自定义智能体][custom-agents]，即针对特定任务和领域定制的 AI 助理。使用自定义智能体可以将团队的专业知识和标准编码为可复用的智能体，从而引导 Copilot 更有效地完成特定类型的工作。

本课探索了以下概念：

- 如何定义自定义智能体。
- 如何在 Copilot Chat 智能体模式中使用自定义智能体。

接下来，[监视并引导智能体的工作][next-lesson]，审查实时更改，并在同一会话中添加浅色模式切换。

## 资源

- [关于自定义智能体][custom-agents]
- [在 IDE 中创建自定义智能体][creating-custom-agents-ide]
- [VS Code 中的自定义智能体][custom-agents-vscode]
- [自定义智能体配置][custom-agents-config]
- [awesome-copilot 上的自定义智能体][awesome-copilot-agents]

---

| [← 上一课：使用 Playwright MCP 服务器测试功能][previous-lesson] | [下一课：监视和管理智能体 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-mcp/
[next-lesson]: ../5-managing-agents/
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[creating-custom-agents-ide]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/create-custom-agents-in-your-ide
[custom-agents-vscode]: https://code.visualstudio.com/docs/copilot/customization/custom-agents
[custom-agents-config]: https://docs.github.com/copilot/reference/custom-agents-configuration
[awesome-copilot-agents]: https://github.com/github/awesome-copilot/tree/main/agents
