---
title: "レッスン 7 - QA エージェントを作成して使う"
description: "Issue の要件、quality-checks skill、Playwright MCP を組み合わせた QA カスタムエージェントを作成します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

`quality-checks` skill を使って自動チェックを実行し、Playwright MCP を使ってブラウザーでフィルター機能を確認しました。これらを明確な QA プロセスを持つカスタムエージェントにまとめましょう。

このレッスンでは、次の内容を学習します。

- カスタムエージェントと指示、skill、MCP ツールとの連携を確認します。
- 再利用可能な品質保証プロファイルを作成して確認します。
- QA エージェントを選択し、フィルター Issue に照らして調査結果をレビューします。

## シナリオ

Tailspin Toys はプルリクエスト (PR) を開く前に、要件、コード品質、自動チェック、テストカバレッジ、ブラウザー動作を一貫してレビューしたいと考えています。カスタムエージェントを使って QA プロセスを調整し、再利用できるレポートを作成できます。

## カスタムエージェントとは

カスタムエージェントは、Markdown プロファイルで定義する専門的な Copilot です。プロファイルにはエージェントの目的、指示、利用できるツールを記述します。このワークショップでは `.github/agents/qa.agent.md` に QA の役割を定義し、Copilot CLI で選択します。

これまで使ったカスタマイズには、それぞれ異なる役割があります。リポジトリの指示はチームの標準を説明します。`quality-checks` skill は繰り返し実行するチェックをまとめます。Playwright MCP はブラウザーツールを提供します。QA プロファイルは、要件を評価して結果を報告するために、それらの機能を Copilot がどう使うかを定義します。これらを置き換えたり、別の会話を始めたりするものではありません。

## QA プロファイルを作成する

機能 PR を開く前に、Copilot に再利用可能な QA プロファイルを作成してもらいます。プロファイルには、QA が実行するチェックと守るべき制約を定義します。

1. Codespace に戻り、フィルター機能の会話が Interactive モードであることを確認します。
2. 既定のエージェントに、新しいカスタムエージェントを作成するよう依頼します。

   ```plaintext
   Create a custom agent named QA in .github/agents/qa.agent.md. It should check features against their issues and agreed requirements, follow the repository instructions, run the quality-checks skill, use Playwright MCP to verify behavior, and add tests when coverage is missing.

   Have it report each requirement as pass, fail, or blocked with supporting evidence. It must ask before changing implementation code, and it must not commit changes or open pull requests.

   Just create the profile for now so I can review it.
   ```

## プロファイルを確認する

新しいエージェントを使う前に、Copilot が意図した QA ワークフローと制約を設定できたか確認します。検証だけを依頼したときに、不完全なエージェントや範囲の広すぎるエージェントが機能を変更することを防げます。

1. `/diff` を入力し、`.github/agents/qa.agent.md` を開きます。
2. フロントマターを読みます。`description` は必須で、`name` は省略できます。`name` を指定すると、エージェントの表示名がわかりやすくなります。
3. プロファイルの指示を読み、QA が要件を確認し、リポジトリの指示に従い、`quality-checks` skill を実行し、Playwright MCP を使うことを確認します。
4. QA が根拠を報告し、実装コードを変更する前に許可を求め、変更をコミットしたりプルリクエストを開いたりしないことを確認します。
5. いずれかの責務や制約が不足している場合は、続行前に既定のエージェントへ修正を依頼します。

## Issue に対して QA を実行する

Copilot CLI は会話の開始時にプロジェクトのエージェントを読み込みます。プロファイルの作成後、同じフィルター機能の会話を再開して QA を選択します。Issue と計画での決定事項を引き続き利用できます。

1. 次のプロンプトでエージェントを有効にします。

   ```plaintext
   /agent QA
   ```

> [!NOTE]
> エージェントを作成した直後は、利用可能なエージェント一覧に表示されない場合があります。上記のコマンドで有効にできます。

2. 次のプロンプトで機能のレビューを QA エージェントに依頼します。

   ```plaintext
   Review the filtering feature against the issue and the decisions in our plan. Is it ready for a PR?
   ```

3. QA エージェントが作業を開始します。
4. 完了後に提示されたレポートを確認します。

## まとめと次のステップ

ワークフローに再利用可能な専門的役割を追加し、その結果を確認しました。このレッスンでは、次の内容を学習しました。

- カスタムエージェントと指示、skill、MCP ツールとの連携。
- 再利用可能な品質保証プロファイルの作成と確認。
- QA エージェントを選択し、フィルター Issue に照らして調査結果をレビューする方法。

実装、skill の更新、QA プロファイル、テスト、検証レポートが揃いました。次に、それらを[機能 PR にまとめ、Agent Merge を使います][next-lesson]。

## リソース

- [Copilot CLI 用のカスタムエージェントを作成する][create-agents]
- [カスタムエージェントの構成][agent-config]

[previous-lesson]: ../6-mcp-playwright/
[next-lesson]: ../8-create-pull-request/
[create-agents]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli
[agent-config]: https://docs.github.com/copilot/reference/custom-agents-configuration
