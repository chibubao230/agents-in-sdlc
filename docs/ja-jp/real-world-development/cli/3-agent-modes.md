---
title: "レッスン 3 - エージェントモード: Plan と Autopilot"
description: "Plan で進め方を決め、Autopilot で Issue からフィルター機能を実装し、Interactive モードで結果をレビューします。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

プロジェクトに小さな機能を追加しました。より大きな変更には、しっかりとしたプロセスが必要です。GitHub Copilot CLI は組織で既に使われている開発フローに合わせて、正しいものを適切な方法で構築できるよう支援します。このレッスンから、エージェントを活用した一般的な開発プロセスに沿って機能を作成します。Issue から新機能を生成し、コードの妥当性と機能の動作を確認して、最後にプロジェクトへマージします。

> [!NOTE]
> 機能開発のワークフローでは、同じ会話とブランチを使います。通常はファイルの種類ごとに異なるブランチや PR を使いますが、ここでは主な概念に集中するため簡略化しています。

このレッスンでは、次の内容を学習します。

- GitHub Issue から Copilot の新しい会話を開始します。
- Plan モードで要件を定義します。
- Autopilot モードで新機能を実装します。
- コードをレビューします。
- 転送されたブラウザーで機能を手動検証します。

この機能の開発を続けながら、リポジトリの指示を更新し、既存の `quality-checks` skill をカスタマイズし、MCP 検証を追加し、QA エージェントを作成して、機能 PR を開きます。

## シナリオ

Tailspin Toys のカタログが拡大し、訪問者がカテゴリや発行元でゲームを絞り込める必要があります。バックログの Issue には機能が説明されていますが、カテゴリの組み合わせなど、実装前に決めておきたい詳細があります。Plan モードで要件を決めてから、範囲を明確にした作業を Autopilot に依頼します。

## 背景

AI コーディングエージェントを開発フローに導入しても、基本は変わりません。むしろ一層重要になります。多くの開発者は、次のような流れで作業します。

1. 実施内容を説明する Issue から始めます。
2. 構築するものの計画を立てます。
3. コードを作成してレビューします。
4. テストを実行してコードを検証します。
5. 新機能を手動で検証します。
6. プルリクエスト (PR) を作成します。
7. コードレビューと継続的インテグレーションが成功した後に、コードをマージします。

> [!NOTE]
> チームや組織によって詳細は異なりますが、ほとんどの開発フローは上記に近いものです。

この標準的な方法に従うと、AI が生成したコードが要件を満たし、手作業で書いたコードと同じ検証プロセスを経ることを確認できます。

## 会話モード

**会話モード**はエージェントに与える自律性の度合いを制御します。<kbd>Shift</kbd>+<kbd>Tab</kbd> を押すとモードを切り替えられます。

- **Interactive**: エージェントと一緒に作業します。エージェントは変更を提案し、続行前にユーザーの入力を待ちます。
- **Plan**: エージェントが最初に計画を作成し、プロジェクトファイルの編集はできません。
- **Autopilot**: エージェントが自律的にコードを書き、テストを実行して、タスクが完了するまで反復します。

最初に Plan モードで計画をレビューし、その後 Autopilot で実装します。

## Issue から始める

フィルター機能に取り組む前に、Codespace に戻り、リポジトリとターミナルの準備ができていることを確認します。

1. Codespace に戻ります。停止している場合は、再起動します。
2. 星評価の PR がマージされたことを確認します。
3. ターミナルが開いていない場合は、<kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押します。
4. `main` を更新してから、フィルター機能用のブランチを作成します。

   ```bash
   git checkout main
   git pull --ff-only
   git checkout -b game-filters-cli
   ```

5. Copilot CLI を起動します。

   ```bash
   copilot --yolo
   ```

6. <kbd>Tab</kbd> を 2 回押して **Issues** タブを開きます。
7. <kbd>A</kbd> を押してすべての Issue を表示します。
8. 矢印キーで **Allow users to filter games by category and publisher** というタイトルの Issue を選択します。
9. <kbd>C</kbd> を押して Issue をプロンプトに追加し、**Session** タブに戻ります。

プロンプトの先頭に `#7` (または同様の番号) が追加されます。`#` を使うと、GitHub の Issue やプルリクエスト (PR) を会話のコンテキストに含められます。

## フィルター機能を計画する

計画を立てると、機能を実装したりタスクを実行したりする前に、進め方を定義できます。複雑な作業では、事前に時間をかけて計画することをお勧めします。Plan モードに切り替え、Copilot に計画の作成を依頼しましょう。

1. <kbd>Shift</kbd>+<kbd>Tab</kbd> を押して Plan モードに切り替えます。プロンプトの下にあるモード表示が **Plan** になっていることを確認します。
2. 前の手順で追加した Issue の後に、次のプロンプトを入力します。

   ```plaintext
   Create a plan for implementing this feature.
   ```

Copilot がプロジェクトを調査し、最適なアプローチを決めて計画を作成します。

3. フィルターの動作について Copilot から質問される場合があります。希望に応じて回答してください。正解は 1 つではありません。
4. 計画ができたら、<kbd>Control</kbd>+<kbd>E</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>E</kbd> (Windows/Linux) を押して計画を展開します。
5. 計画をスクロールして確認します。
6. 自分の判断と一致しない箇所があれば、修正を依頼します。

## Autopilot を承認する

計画を作成してレビューできたら、実装に進みます。Autopilot で Copilot に作業を任せましょう。

Autopilot では、Copilot が完了したと判断するまでタスクを反復して進めます。

1. **Accept plan and build on autopilot (recommended)**、または使用中のバージョンにある同等のオプションを選択します。
2. プロンプトの下にあるモード表示が **Autopilot** になっていることを確認します。
3. Copilot が計画に従って作業を反復し、コードを生成してテストを実行する様子を確認します。

> [!NOTE]
> 承認するとすぐに実装が始まる場合があります。まず計画をレビューしてください。依存関係の不足やポートの競合が Copilot から報告された場合は、チェックが完了したと見なす前に環境の問題を解決します。

## 実装をレビューして検証する

生成されたコードは、ほかのコードと同じようにマージ前にレビューします。コードを確認し、サイトを実行して期待どおりに見えることも検証します。

1. <kbd>Shift</kbd>+<kbd>Tab</kbd> を押して Interactive モードにします。モード表示が **Plan** または **Autopilot** ではないことを確認します。
2. `/diff` を入力し、フィルター機能の実装とテストを確認します。
3. 結果を Issue と計画で決めた内容に照らして確認します。
4. コードのレビューが終わったら <kbd>Esc</kbd> を押して差分表示を閉じます。
5. プロジェクトのチェック結果を確認し、失敗があれば Copilot に解決を依頼します。

## 新機能を確認する

コードに問題がなくても、実行できるとは限りません。前と同じようにアプリを起動し、Codespaces の転送ポートからサイトを開きましょう。

1. Copilot にアプリの起動を依頼します。

   ```plaintext
   Start the app so I can try the filtering feature in my browser. Tell me the URL and leave the server running.
   ```

2. Codespaces からポート `4321` が使用可能と通知されたら、**Open in Browser** を選択します。
3. カテゴリ、発行元、および計画で決めた組み合わせによるフィルターを試します。
4. リセット動作と結果が空の場合の動作が、Issue と決定した内容に合うことを確認します。
5. Codespace に戻り、Copilot に起動した開発サーバーの停止を依頼します。

## まとめと次のステップ

異なる会話モードを使い、機能を作成してレビューしました。このレッスンでは、次の作業を行いました。

- GitHub Issue から Copilot の新しい会話を開始しました。
- Plan モードで要件を定義しました。
- Autopilot モードで機能を実装しました。
- コードをレビューしました。
- 転送されたブラウザーで機能を手動検証しました。

次に、コード生成の方法を詳しく確認し、文書化された手法に従うよう[カスタム指示を使います][next-lesson]。

## リソース

- [GitHub Copilot CLI の Autopilot][autopilot]
- [Copilot CLI コマンドリファレンス][cli-reference]

[autopilot]: https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[previous-lesson]: ../2-add-star-rating/
[next-lesson]: ../4-custom-instructions/
