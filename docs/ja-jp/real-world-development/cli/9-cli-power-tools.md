---
title: "レッスン 9 - GitHub Copilot CLI のスラッシュコマンド"
description: "コンテキストの管理、モデルの選択、セッションの共有、cloud agent への任意の委任に使うスラッシュコマンドを確認します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

一般的な CLI ツールと同じように、GitHub Copilot CLI にも操作に使うスラッシュコマンドが多数あります。高度な機能、内部の状態、追加の構成オプションを利用できます。すでに `/diff`、`/mcp`、`/skills`、`/agent`、`/pr` などを使いました。ほかの便利なコマンドも確認しましょう。

このレッスンでは、次の内容を学習します。

- `/context` と `/compact` を使い、Copilot が会話のコンテキストを管理する方法を確認します。
- `/model` を使い、利用可能なモデルを確認します。
- `/share` でセッションをエクスポートまたは共有する方法を学びます。
- 並列作業、worktree、cloud agent への委任に使う任意のコマンドを確認します。

## シナリオ

CLI の主要なワークフローを完了しました。ここでは、コンテキストの管理、モデルの切り替え、セッションの共有、[Copilot cloud agent][about-cloud-agent]への任意の委任など、追加機能を確認します。

## Copilot CLI のコンテキストを確認する

大規模または複雑なタスクに取り組むと、モデルのコンテキストウィンドウの上限に達する場合があります。Copilot CLI は必要に応じて会話を自動的に圧縮します。また、スラッシュコマンドを使って、自分で状態を確認したり圧縮したりできます。

1. Codespace に戻り、Copilot CLI がまだ開いていない場合はリポジトリのルートから起動します。
2. 次のコマンドを入力します。

   ```plaintext
   /context
   ```

3. モデル、現在のトークン使用量、システム指示、ツール、メッセージ、空き領域に分けられたコンテキストを確認します。
4. 会話を圧縮します。

   ```plaintext
   /compact
   ```

5. もう一度 `/context` を入力して結果を比較します。会話がすでに短い場合は、大きな変化がないこともあります。

> [!NOTE]
> コンテキストウィンドウがいっぱいになると、Copilot CLI は自動的にコンテキストを圧縮します。タイミングを自分で決めたい場合は `/compact` を使います。関係のない別のタスクを始めるため会話をリセットする場合は、`/clear` または `/new` を使います。

## モデルを選ぶ

モデルごとに得意分野が異なり、開発者によって好みも異なります。Copilot CLI では、利用できるモデルを一覧表示して選択できます。

1. 次のコマンドを入力します。

   ```plaintext
   /model
   ```

2. 利用できるモデルと使用量に関する情報を確認します。
3. 現在のモデルを使うか、別のモデルを選ぶか、<kbd>Esc</kbd> を押して一覧を閉じます。

## セッションを共有する

チームで協力し、学びを共有すると、AI ツールをより効果的に使えます。`/share` コマンドを使うと、セッションを Markdown または HTML ファイルにエクスポートしたり、共有リンクを作成したり、GitHub gist に公開したりできます。

1. `/help` を入力し、インストールされているバージョンの `/share` オプションを確認します。
2. セッションを共有する場合は、用途に合う共有先を選択します。たとえば `/share file` はローカルの Markdown ファイルにエクスポートします。
3. 共有または公開する前に、エクスポート内容を確認します。セッションのエクスポートにはプロンプト、応答、プロジェクトの詳細が含まれる場合があります。

リンクや gist の公開は任意です。チームが共有する意図のないリポジトリや会話の内容は公開しないでください。

## 省略可能: 作業を拡張または委任する

ワークショップの主要な内容は完了しました。Copilot CLI には、大きなタスクに使える次のコマンドもあります。

- `/fleet` は独立したサブタスクをサブエージェントに分割し、並列に実行できます。
- `/worktree` は別のタスク用に分離された Git worktree を作成できます。
- `/delegate` はタスクを Copilot cloud agent に送り、非同期に作業させることができます。プルリクエストが作成される場合もあります。

これらのコマンドを使うと、追加の worktree やリモート作業が発生する可能性があるため、省略可能です。試す場合は、新しく範囲を明確にしたタスクから始め、通常のワークフローで結果をレビューします。非同期エージェントの作業について詳しく知りたい場合は、[cloud agent のワークショップ][cloud-workshop]に進みます。

## まとめと次のステップ

Copilot CLI のスラッシュコマンドを使い、設定を変更し、セッションを共有し、内部の状態を確認しました。このレッスンでは、次の内容を学習しました。

- `/context` と `/compact` でコンテキスト管理を確認しました。
- `/model` で利用できるモデルを確認しました。
- `/share` でセッションをエクスポートまたは共有する方法を学びました。
- 並列作業、worktree、cloud agent への委任に使う任意のコマンドを確認しました。

Copilot CLI には、ほかにも多くのスラッシュコマンドや機能があります。ここまでの学習を[振り返り][next-lesson]、次のステップを確認して締めくくりましょう。

## リソース

- [Copilot CLI コマンドリファレンス][cli-reference]
- [Copilot CLI のコンテキスト管理][context-management]
- [Copilot cloud agent について][about-cloud-agent]

[previous-lesson]: ../8-create-pull-request/
[next-lesson]: ../10-review/
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[context-management]: https://docs.github.com/copilot/concepts/agents/copilot-cli/context-management
[about-cloud-agent]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-workshop]: ../../cloud/
