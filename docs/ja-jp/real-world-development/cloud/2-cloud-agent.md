---
title: "演習 2 - GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: カスタム指示][previous-lesson] |
|:--|

技術的負債に悩んでいない組織はほとんどないでしょう。未解決のセキュリティ問題、更新が必要なレガシーコード、実装する時間がなくバックログに残り続ける機能リクエストなどが考えられます。GitHub Copilot の cloud agent は、コードの更新や機能追加などのタスクを自律的に実行できます。作業が完了すると、人間の開発者がレビューできるドラフト PR を作成します。面倒な作業を任せて開発を加速し、開発者がより大きな課題に集中できるようにします。

この演習では、Copilot cloud agent を使って次の内容を学習します。

- コード生成環境をカスタマイズする。
- 安全に操作する方法を確認する。
- Issue の範囲を明確にする重要性を理解する。
- Issue を Copilot に割り当てる。

## シナリオ

Tailspin Toys には解決したい技術的負債があります。最初のサイトを作成した請負業者がドキュメントを十分に整備しなかったため、まずアプリケーション内のすべてのエクスポートされた関数に TSDoc ドキュメントコメントを追加します。

また、デザインチームはゲームを見つけやすくしたいと考えています。ゲームの詳細ページに同じカテゴリの別のゲームを表示し、閲覧を続けられるようにします。完成度の高い実装はまだ必要ありません。UX の受け入れテストに使えるものを用意します。この作業は現在のブロッカーですが、より優先度の高い Issue もあります。

どちらも優先順位が下がりやすいタスクの例であり、Copilot cloud agent に割り当てるのに適しています。cloud agent が非同期で作業している間、開発者は別の作業に集中し、後から戻って Copilot の成果をレビューできます。

## GitHub Copilot cloud agent について

[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) は、人間の開発者と同じようにバックグラウンドでタスクを実行できます。[GitHub Issue を Copilot に割り当てる](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)など、複数の方法で作業を依頼できます。割り当てると、Copilot は進捗を追跡するドラフトプルリクエストを作成し、環境をセットアップして作業を始めます。実行中でも完了後でもセッションを確認できます。提案された解決策をレビューできる状態になると、プルリクエストで通知されます。

## 範囲を明確にした指示の重要性

GitHub Copilot に魔法はありません。考える作業を省略できるわけではありません。一見簡単な操作にも、詳しく見ると複雑さがあります。[Copilot cloud agent に依頼するタスクの範囲を明確にする][cloud-agent-best-practices]ことが大切です。AI ペアプログラマーとして扱い、段階を踏んで学び、試し、調整しましょう。生成 AI を導入しても、ソフトウェア開発の基本は変わりません。

## このリポジトリのカスタム指示

前の演習では、カスタム指示と Copilot の動作への影響を確認しました。カスタム指示の演習をすでに行った場合は、`.github/copilot-instructions.md` がどのように使われるかを確認できています。まだの場合は、ここで簡単に確認しましょう。cloud agent に作業を割り当てる前に、このリポジトリに含まれている指示ファイルを読み取り専用で確認し、後でその影響を見分けられるようにします。

リポジトリの GitHub Web UI、または起動済みの codespace で、次のファイルを開きます。

- `.github/copilot-instructions.md` - **Code standards** セクション、特に TypeScript 規約と TSDoc ドキュメントコメントに関する要件を確認します。
- `.github/instructions/unit-tests.instructions.md` - `applyTo` フロントマターを確認します。この指示は `**/*.test.ts` ファイルに適用されます。

次のセクションで `Code lacks documentation` Issue を cloud agent に割り当てるとき、生成されたプルリクエストに TSDoc コメント、TypeScript 規約、コメントヘッダーが反映されているか確認します。後の演習で PR をレビューするときにも取り上げます。

## Copilot cloud agent の開発環境をセットアップする

誰が作業する場合でも、コード作成には通常、特定の環境や状態を整えるためのセットアップスクリプトが必要です。SWE と同様にタスクを実行する Copilot に作業を割り当てるときも同じです。

cloud agent は、作業環境として [GitHub Actions][github-actions] を使います。`.github/workflows/copilot-setup-steps.yml` に設定する[専用セットアップワークフロー][setup-workflow]を作成すると、作業開始前に必要な開発ツールや依存関係を用意できます。このラボでは学習に集中できるよう、あらかじめ設定されています。単一の Astro アプリに必要な Node.js、プロジェクトの依存関係、エンドツーエンドテスト用のブラウザーバイナリ、マイグレーションとシードが済んだ SQLite データベースを Copilot が利用できます。

```yaml
name: "Copilot Setup Steps"

# リポジトリの "Actions" タブからセットアップ手順をテストできます。
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # 手順に必要な最小限のアクセス許可を設定します。Copilot には操作用の独自のトークンが付与されます。
    permissions:
      # 依存関係のインストールなど、セットアップ手順でリポジトリをクローンする場合は `contents: read` 権限が必要です。手順でクローンしない場合は、完了後に Copilot が自動的にクローンします。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # フロントエンド / アプリのセットアップ - Node.js (アプリ全体が Astro + Drizzle/libSQL になりました)
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # ローカル SQLite データベースをマイグレーションしてシードし、ビルドとテストで使うデータを用意します。
      - name: Set up the database
        run: npm run db:setup
```

ほかの GitHub ワークフローファイルと同様ですが、いくつか特徴があります。

- `copilot-setup-steps` という単一のジョブがあります。このジョブは、Copilot がプルリクエストの作業を始める前に GitHub Actions で実行されます。
- `workflow_dispatch` トリガーを使うと、リポジトリの **Actions** タブからワークフローを手動で実行できます。Copilot の実行を待たずにワークフローをテストするのに便利です。

## ドキュメントを追加する

ドキュメントの重要性はよく知られていますが、ほとんどのプロジェクトでは情報が古いか、そもそも不足しています。このような技術的負債は放置されやすく、生産性を下げ、コードベースの保守や新しい開発者の参加を難しくします。Copilot はドキュメント作成を得意とするため、cloud agent に任せるのに適した作業です。バックグラウンドで必要なドキュメントを生成してもらい、後の演習で成果をレビューします。

1. 新しいブラウザータブで github.com のリポジトリを開きます。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい Issue の画面を開きます。
4. **Blank issue** を選択して新しい Issue を作成します。
5. **Title** に `Code lacks documentation` と入力します。
6. **Description** に次の内容を入力します。

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. **Create** を選択して Issue を作成します。
8. 右側の **Assign to Copilot** を選択して割り当て画面を開きます。

  ![Copilot を Issue に割り当てる画面](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。
10. **Pull Requests** タブを選択します。
11. 作成されたプルリクエスト (PR) を開きます。タイトルは `[WIP]: Code lacks documentation` のようなものです。新しい PR が表示されない場合は、少し待ってからブラウザーを更新します。
12. 数分後、Copilot が TODO リストを作成したことを確認します。

> [!NOTE]
> Copilot が PR に TODO リストを表示するまで数分かかることがあります。Copilot は先ほどのワークフローを実行して環境を作成し、プロジェクトを分析して、問題への最適な取り組み方を判断しています。

13. リストと Copilot が実行するタスクを確認します。
14. プルリクエストのタイムラインを下にスクロールし、Copilot が Issue に取り組み始めたことを示す更新を確認します。
15. **View session** ボタンを選択します。

  ![Copilot セッションの表示](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 更新された表示が現れるまで、ウィンドウの再読み込みが必要な場合があります。

16. 実行中のセッションをスクロールし、Copilot がどのように問題を解決しているか確認します。コードの調査や状態の把握、計画を考えて決定するための一時停止、コードの作成などを確認できます。

完了まで数分かかることがあります。Copilot cloud agent の主な目的の 1 つは、タスクを非同期に実行して、別の作業に集中できるようにすることです。この機能を活用し、もう 1 つタスクを Copilot cloud agent に割り当ててから、アプリケーションの機能追加に取り組みます。

## ゲーム詳細ページに関連ゲームを追加する

GitHub Copilot cloud agent の利点の 1 つは作業を分担できることです。デザインチーム向けに関連ゲームを追加する作業に時間がかからないとしても、その間に別のタスクを進められます。Copilot cloud agent に割り当てましょう。

1. github.com のリポジトリに戻ります。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい Issue の画面を開きます。
4. **Blank issue** を選択して空のテンプレートを使います。
5. **Title** に `Show related games on the game details page` と入力します。
6. **Description** に次の内容を入力します。

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. **Create** を選択して Issue を作成します。
8. 右側の **Assign to Copilot** を選択して割り当て画面を開きます。

  ![Copilot を Issue に割り当てる画面](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

まもなく、Copilot が作業を開始したことを示す 👀 が Issue の最初のコメントに表示されます。

![Issue に取り組んでいることを示す目の絵文字を Copilot が使っている画面](../../../_images/ex4-issue-eyes-emoji.png)

Copilot は 2 つ目の依頼に取り組んでいます。Copilot cloud agent は SWE と同じように作業するため、完了まで常に監視する必要はありません。完了後にレビューできます。次は、カスタムエージェントの作成と使用に取り組みましょう。

## まとめと次のステップ

[GitHub Copilot cloud agent][copilot-agents]を使うと、Issue を Copilot に割り当てて非同期に実行できます。技術的負債の解消、新機能の作成、別のフレームワークへのコード移行などに活用できます。

この演習では、次の内容を学習しました。

- コード生成環境をカスタマイズする。
- 安全に操作する方法を確認する。
- Issue の範囲を明確にする重要性を理解する。
- Issue を Copilot に割り当てる。

cloud agent がバックグラウンドで作業している間に、カスタムエージェントの作成と使用に取り組みます。[Copilot cloud agent では MCP サーバー][cloud-agent-mcp]や、前のモジュールで確認したカスタム指示も利用できます。

## リソース

- [Copilot cloud agent について][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent のセットアップワークフローのベストプラクティス][cloud-agent-best-practices]

| [次のレッスン: カスタムエージェント →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
