---
title: "レッスン 10 - 振り返りと次のステップ"
description: "Copilot CLI のワークフロー、2 件のプルリクエスト、再利用可能なカスタマイズ、追加リソースを振り返ります。"
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

GitHub Copilot CLI を使って、Tailspin Toys の一連のワークフローに取り組みました。次の作業を行いました。

- Codespace を準備し、Copilot CLI をインストールして、プロジェクトを確認し、事前に登録されたフィルター機能の Issue を見つけました。
- 星評価を追加し、転送されたブラウザーで結果を確認し、最初のプルリクエスト (PR) を手動でマージしました。
- フィルター機能の Issue から始め、Plan モードで進め方を決め、Autopilot モードで実装し、Interactive モードでレビューしました。
- カスタム指示でエージェントを導き、既存の `quality-checks` skill をカスタマイズし、ユニットテスト、lint、型チェックに使いました。
- Playwright Model Context Protocol (MCP) サーバーを追加し、実際のブラウザーでフィルター機能を確認しました。
- 品質保証 (QA) 用のカスタムエージェントを作成して選択し、要件、カバレッジ、skill の結果、ブラウザーでの確認結果を評価しました。
- フィルター機能全体をレビューし、Agent Merge によるフィルター PR のマージを許可しました。
- コンテキスト、モデル、共有、cloud agent への任意の委任に使うスラッシュコマンドを確認しました。

## リリースしたもの

このワークショップでは、更新後の `main` から作成した別々のブランチで、2 つの PR を作成します。

1. **星評価:** ゲームカードに既存の `starRating` と、未評価であることを明示する表示を追加します。
2. **フィルターと品質のワークフロー:** フィルターを実装し、指示を更新して機能に適用し、`quality-checks` のレポートをカスタマイズし、QA プロファイルと関連テストを追加します。

フィルター機能の計画から PR の作成までは、同じ会話とブランチを使いました。ワークショップを簡潔にするため、1 件の PR にまとめています。

## さまざまな検証方法

自動テスト、自分で行うブラウザー確認、MCP を使った Copilot のブラウザー操作など、複数の方法でコードを確認しました。`quality-checks` skill はユニットテスト、lint、型チェックを実行し、カスタマイズした形式で結果を報告しました。QA エージェントは PR の前にこれらの結果を要件とテストカバレッジのレビューと組み合わせました。

追加するテストは実際の不足を補うものであるべきです。QA の実行結果として、新しいテストを追加する必要がない場合もあります。マージを許可する前に、コードと根拠をレビューしてください。変更があった場合は、影響する検証の根拠を更新します。

## ベストプラクティス

Copilot に与えるコンテキストとツールは、その作業に影響します。このワークショップでは、指示の更新、skill のカスタマイズ、QA プロファイルの作成、MCP サーバーの設定を行いました。これらのカスタマイズは会話を越えて再利用し、チームのニーズに合わせて調整できます。指示は標準を設定し、skill は繰り返し行うタスクを説明し、カスタムエージェントは専門的な役割を定義し、MCP サーバーは外部ツールに接続します。エージェントの概要だけでなく、実際の変更やツールの実行結果を確認してください。

タスクに合わせて**モードとモデル**を選びます。実装前に進め方を考える場合は **Plan**、対象を絞った変更に取り組みながら確認する場合は **Interactive**、範囲が明確なタスクには **Autopilot** を使います。定型的な編集には高速なモデル、複雑な作業には高性能なモデルを選びます。

インフラストラクチャと同じように、コンテキストも重要です。何を、なぜ、どのように作りたいのかを明確に説明すると、出力が大きく変わります。

## さらに試す

主要なワークフローを学習しました。次の CLI 機能も確認できます。

- `/review` を使い、コードレビューエージェントに変更を分析してもらいます。
- `/rubber-duck` を使い、問題について会話して別の視点を得ます。
- `/fleet` を使い、独立したサブタスクを並列に実行します。
- `/worktree` を使って別のタスクを分離します。
- `/delegate` を使って Copilot cloud agent にタスクを送信します。

## 次のステップ

どのツールでも、上達するには使い続けることが大切です。本番コード、趣味のコード、何年も考えていたけれどまだ作っていない小さなアプリなどに活用してください。学びをチームで共有し、チームからも学びましょう。ドキュメントも確認してください。

ターミナルから Tailspin Toys をさらに拡張する場合は、任意の [Foundry Backer Concierge シリーズ][foundry]に進みます。ほかの環境と比較するには、[VS Code ワークショップ][vscode]、[GitHub Copilot app ワークショップ][app]、[Copilot cloud agent ワークショップ][cloud]を確認してください。

## リソース

- [GitHub Copilot CLI について][about-cli]
- [Copilot CLI コマンドリファレンス][cli-reference]
- [Copilot CLI をカスタマイズする][customize-cli]
- [Copilot CLI でプルリクエストを管理する][manage-prs]

[previous-lesson]: ../9-cli-power-tools/
[foundry]: ../8-foundry-agent/
[vscode]: ../../vscode/
[app]: ../../app/
[cloud]: ../../cloud/
[about-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[customize-cli]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests
