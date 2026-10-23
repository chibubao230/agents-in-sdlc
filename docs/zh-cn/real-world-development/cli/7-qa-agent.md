---
title: "第 7 课 - 创建并使用 QA 智能体"
description: "创建 QA 自定义智能体，将议题要求、quality-checks 技能和 Playwright MCP 结合起来。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

你已使用 `quality-checks` 技能运行自动化检查，并使用 Playwright MCP 在浏览器中观察筛选体验。现在将这些能力整合到一个自定义智能体中，并为它定义清晰的 QA 流程。

本课将学习如何：

- 了解自定义智能体如何配合说明、技能和 MCP 工具工作。
- 创建并检查可复用的质量保证配置。
- 选择 QA 智能体，并根据筛选议题审查其发现。

## 场景

创建拉取请求（PR）前，Tailspin Toys 希望对需求、代码质量、自动化检查、测试覆盖率和浏览器行为进行一致的审查。自定义智能体可以协调 QA 流程，并生成可复用的报告。

## 什么是自定义智能体？

自定义智能体是在 Markdown 配置文件中定义的 Copilot 专用角色。配置文件描述智能体的用途、说明和可用工具。本研讨会将通过 `.github/agents/qa.agent.md` 定义 QA 角色，并在 Copilot CLI 中选择它。

前面使用的自定义项各有不同用途：存储库说明描述团队标准；`quality-checks` 技能封装可重复执行的检查；Playwright MCP 提供浏览器工具；QA 配置则告诉 Copilot 如何使用这些能力评估要求并报告发现。它不会取代这些能力，也不需要另开对话。

## 创建 QA 配置

创建功能 PR 之前，让 Copilot 创建一个可复用的 QA 配置。配置将定义 QA 执行的检查及其必须遵循的边界。

1. 返回 Codespace，确认筛选对话处于 Interactive 模式。
2. 让默认智能体创建新的自定义智能体：

   ```plaintext
   Create a custom agent named QA in .github/agents/qa.agent.md. It should check features against their issues and agreed requirements, follow the repository instructions, run the quality-checks skill, use Playwright MCP to verify behavior, and add tests when coverage is missing.

   Have it report each requirement as pass, fail, or blocked with supporting evidence. It must ask before changing implementation code, and it must not commit changes or open pull requests.

   Just create the profile for now so I can review it.
   ```

## 检查配置

使用新智能体前，先审查其配置，确认 Copilot 已记录预期的 QA 工作流和边界。这样可以避免智能体在你只想验证功能时，执行不完整或范围过大的操作。

1. 输入 `/diff` 并打开 `.github/agents/qa.agent.md`。
2. 阅读 frontmatter。`description` 为必填项；`name` 是可选项，但添加后可为智能体提供清晰的显示名称。
3. 阅读配置说明，确认 QA 会从需求开始，遵循存储库说明，运行 `quality-checks` 技能，并使用 Playwright MCP。
4. 确认 QA 会报告支持证据、在修改实现代码前询问，并且不会提交更改或创建拉取请求。
5. 如果生成的配置缺少上述职责或边界，请先让默认智能体修改，再继续。

## 根据议题运行 QA

Copilot CLI 会在对话启动时加载项目智能体。创建配置后，恢复同一个筛选对话，再选择 QA，以便它使用已有的议题和规划决策。

1. 使用以下提示启用智能体：

   ```plaintext
   /agent QA
   ```

> [!NOTE]
> 刚创建的智能体可能暂时不会出现在可用智能体列表中，但仍可使用上述命令启用。

2. 使用以下提示让 QA 智能体审查功能：

   ```plaintext
   Review the filtering feature against the issue and the decisions in our plan. Is it ready for a PR?
   ```

3. QA 智能体会开始工作。
4. 完成后阅读它提供的报告。

## 总结和后续步骤

你已在工作流中添加可复用的专业角色并审查了它的工作。本课中，你：

- 了解了自定义智能体如何配合说明、技能和 MCP 工具工作。
- 创建并检查了可复用的质量保证配置。
- 选择了 QA 智能体，并根据筛选议题审查了其发现。

现在，实现、技能更新、QA 配置、测试和验证报告都已准备就绪。接下来，[将它们汇总到功能 PR 中，并使用 Agent Merge][next-lesson]。

## 资源

- [为 Copilot CLI 创建自定义智能体][create-agents]
- [自定义智能体配置][agent-config]

[previous-lesson]: ../6-mcp-playwright/
[next-lesson]: ../8-create-pull-request/
[create-agents]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli
[agent-config]: https://docs.github.com/copilot/reference/custom-agents-configuration
