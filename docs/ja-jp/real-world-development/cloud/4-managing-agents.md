---
title: "演習 4 - エージェントの監視と管理"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

これまでの演習で、Copilot cloud agent にユーザー体験の向上と機能追加に関する 3 つのタスクを依頼しました。cloud agent は非同期かつ自律的に動作しますが、タスクを監視することも重要です。

GitHub.com の[エージェントページ][agents-page]など、cloud agent に割り当てたタスクを管理するツールがいくつもあります。この mission control では、オープンなプルリクエスト (PR) があるすべてのエージェントタスクを確認できます。実行された操作を調べたり、進行中のセッションを方向付けたりできます。

このレッスンでは、次の内容を学習します。

- エージェントページで cloud agent のタスクを監視します。
- 実行中のセッションを方向付けて、機能を追加で依頼します。

## シナリオ

エージェントにハイコントラストモードの作成を依頼した後、チームはライトモードも追加するのがよいと考えました。サイトのスタイルと切り替え機能を更新しているため、ハイコントラストに加えてライトモードも実装するよう、エージェントの作業を方向付けます。

## Copilot cloud agent のタスクを確認する

Copilot cloud agent に割り当てたタスクの現在の状態を確認します。

1. [Copilot エージェントページ](https://github.com/copilot/agents)を開きます。
2. メインペインと左ペインにあるタスクの一覧を確認します。次のようなタスクが表示されます。
   - コードベースのドキュメントを更新する。
   - ゲームを変更する API を生成する。
   - Web サイトにハイコントラストモードを追加する。
3. 実行中のタスクを 1 つ選択し、Copilot が実施した作業を確認します。次のような作業が含まれることがあります。
   - リポジトリをチェックアウトする。
   - Copilot の作業環境を作成する。
   - MCP サーバーをセットアップする。
   - 割り当てられたタスクを完了するための各種作業を実行する。

> [!NOTE]
> 表示される手順は、Copilot の作業状態や進め方によって異なります。

4. 右側に表示される PR ペインも確認します。PR と変更されたファイルを確認し、進捗を監視できます。

## cloud agent の作業を方向付ける

実行中のタスクを確認できたので、ハイコントラストモードの作業中にライトモードの切り替えも追加するよう Copilot に依頼します。

1. ハイコントラストモードの追加に関するセッションを選択します。表示されるタイトルは Copilot が付けた名前や作業状態によって異なります。

    ![mission control のアクセシビリティセッション](../../../_images/ex6-accessibility-session.png)

2. セットアップが終わり、実装が始まったことを示すメッセージが表示されるまで、数分間セッションを確認します。
3. **Steer active session while Copilot is working** ダイアログに、次のプロンプトを入力します。

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![Copilot エージェントページのタスクで、Steer active session while Copilot is working ダイアログが強調表示されたスクリーンショット](../../../_images/ex6-steer-cloud-agent-task.png)

4. <kbd>Enter</kbd> を押してプロンプトを送信します。
5. Copilot がプロンプトを受け取り、作業に反映することを確認します。

## Copilot に作業を続けてもらう

Copilot は更新されたタスクに取り組みます。メッセージを送信した時点で進行中だった手順が終わると、新しい依頼を作業に取り込みます。

前と同じように完了まで数分かかります。その間に、これまで学習した内容を振り返りましょう。

## まとめと次のステップ

GitHub Copilot cloud agent のタスクを監視し、方向付ける中心的な場所である Copilot エージェントページを確認しました。この mission control では、進行中と完了済みのタスクを追跡し、作業内容をレビューし、進行中のタスクの範囲を変更したり、追加のガイダンスを与えたりできます。

この演習では、次の内容を学習しました。

- mission control とエージェントページを使って、cloud agent のタスクを監視しました。
- 実行中のセッションを方向付け、機能の追加を依頼しました。

Copilot がアクセシビリティ機能を実装している間に、作成されたプルリクエストを[反復して改善する][next-lesson]方法を確認しましょう。

## リソース

- [Copilot エージェントページ][agents-page]
- [カスタムエージェントについて][custom-agents]

---

| [← 前のレッスン: カスタムエージェント][previous-lesson] | [次のレッスン: Copilot の作業を反復して改善する →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
