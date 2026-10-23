---
title: "レッスン 8 - 機能 PR を作成してマージする"
description: "フィルター機能、指示、skill の更新、QA プロファイル、テストをまとめてレビューし、PR を作成して Agent Merge を使います。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

フィルター機能の実装、指示の更新、skill の更新、品質保証 (QA) プロファイル、テストは、1 つのブランチに保存されています。まとめてレビューし、プルリクエストを開きましょう。星評価のプルリクエスト (PR) は自分でマージしましたが、今回は **Agent Merge** にマージの処理を任せます。

> [!NOTE]
> 通常であれば、機能、指示の更新、skill の更新、QA エージェントは、いくつかの PR に分けます。ワークショップを簡潔にするため、フィルター機能と品質向上の作業を 1 つの会話とブランチにまとめ、1 件の PR に含めました。

このレッスンでは、次の内容を学習します。

- Agent Merge とは何か、マージまでのライフサイクルを自動化する仕組みを理解します。
- 機能の変更全体と検証の根拠を確認します。
- フィルター機能の PR を作成します。
- レビュー後にのみ Agent Merge を有効にし、PR がマージされたことを確認します。

## シナリオ

フィルター機能のワークフロー全体で、Copilot を使って機能の計画、実装、検証を行いました。Tailspin Toys は、開発者がマージを許可する権限を保ちながら、残りの PR 作業を自動化したいと考えています。

## Agent Merge について

**Agent Merge** は、プルリクエストをマージ可能な状態にするために必要な作業を自動化します。有効にすると、Copilot は PR を妨げている問題 (失敗した継続的インテグレーション (CI) チェックの修正、レビューコメントへの対応、必要に応じたリベースなど) に取り組みます。その後、リポジトリが許可していれば GitHub の自動マージを有効にします。

これまでは **Merge pull request** を自分で選択していました。Agent Merge にその作業を任せることができます。有効化する前に、作業をレビューしてマージできる状態であることを確認してください。

## Agent Merge で PR を管理する

コードを作成できたので、まとめてレビューし、PR を作成して、残りの処理を Agent Merge に任せます。

1. Codespace に戻ります。
2. `/agent` を入力してエージェントダイアログを開きます。
3. 一覧から **Default** を選択し、<kbd>Enter</kbd> を押します。
4. `/pr create` コマンドで PR を作成します。
5. `/pr agentmerge` コマンドで Agent Merge を有効にします。
6. Copilot は PR の継続的インテグレーションを監視し、すべて成功するとマージします。
7. Copilot から `"PR #14 was squash-merged successfully."` のようなメッセージが表示されたことを確認します。

> [!IMPORTANT]
> Agent Merge は、必須の承認、ブランチ保護、マージキュー、リポジトリ設定、アクセス許可を回避しません。処理がブロックされた場合は、報告された理由を確認し、リポジトリで許可される場合はレビュー済みの PR を手動でマージします。

## まとめと次のステップ

コードの生成、テストと検証に続いて、プルリクエストの処理も自動化しました。このレッスンでは、次の内容を学習しました。

- Agent Merge とマージのライフサイクルを自動化する仕組み。
- 機能の変更全体と検証の根拠を確認する方法。
- フィルター機能の PR の作成。
- レビュー後に Agent Merge を有効にし、マージを確認する方法。

次に、コンテキスト、モデル、共有、cloud agent への任意の委任に役立つ [Copilot CLI のスラッシュコマンド][next-lesson]を確認します。

## リソース

- [Copilot CLI でプルリクエストを管理する][manage-prs]
- [Copilot CLI コマンドリファレンス][cli-reference]

[previous-lesson]: ../7-qa-agent/
[next-lesson]: ../9-cli-power-tools/
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
