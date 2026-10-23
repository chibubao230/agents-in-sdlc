---
title: "演習 5: GitHub Copilot の作業を反復して改善する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

## 作業をレビューする

このラボでは、ユーザー体験の向上と機能追加を目的に、GitHub Copilot と複数のタスクに取り組んできました。コードへのドキュメント追加、デザインチームが改善案を検討できる関連ゲーム機能の作成、ハイコントラストとライトモードの切り替えを含むアクセシビリティ機能の実装を依頼しました。コードの変更を確認し、必要に応じて Copilot にフィードバックを送り、改善してもらいましょう。

### シナリオ

これまで何度も確認したように、生成 AI を使ってもソフトウェア設計や DevOps の基本は変わりません。生成されたコードをレビューし、通常の DevOps プロセスに従う必要があります。チーム向けのレビューを有効にする前に、ドキュメント、関連ゲーム機能、アクセシビリティ機能について GitHub Copilot が提案した内容を確認します。

## セキュリティと GitHub Copilot cloud agent

Copilot cloud agent は非同期に、監視されずにタスクを実行するため、安全を保つためのセキュリティ制約があります。

- Copilot はリポジトリに対する読み取りアクセス権を持ち、コード用に使用するブランチ**のみ**に書き込みできます。
- cloud agent は GitHub Actions 内で実行され、個別の一時的な環境を作成して作業します。
- GitHub Actions のワークフローを実行するには、人間の承認が必要です。
- MCP サーバーを含め、[外部リソースへのアクセスは既定で制限されています](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)。

## 生成されたドキュメントをレビューする

まず、GitHub Copilot cloud agent が作成した最初のプルリクエスト (PR)、つまりコードへのドキュメント追加を確認します。GitHub.com の標準的な PR インターフェイスを使います。

> [!NOTE]
> PR を開いたとき、GitHub Copilot がファイアウォールによってブロックされたという警告が表示される場合があります。既定では Copilot の外部リソースへのアクセスが制限されており、外部 MCP サーバーへの呼び出しも含まれるため、これは**想定された動作**です。必要に応じて、[Copilot cloud agent のファイアウォールをカスタマイズまたは無効化][agent-firewall]できます。

1. github.com のリポジトリに戻ります。
2. **Pull Requests** を選択して、プルリクエストの一覧を開きます。
3. **Add missing documentation** などのタイトルが付いたプルリクエストを開きます。

> [!NOTE]
> Copilot がまだ作業中の場合、プルリクエストのタイトルに **[WIP]** が表示されます。その場合は Copilot が作業を終えるまで待ちます。数分かかることがあります。その間に休憩したり、ここまでに学んだ内容を振り返ったりしてください。

4. プルリクエストの準備ができたら、**Files changed** タブを選択して変更をレビューします。

    ![Files changed タブ](../../../_images/shared-pr-files-changed.png)

5. TSDoc ドキュメントコメントなど、新しく追加されたドキュメントを含むコードを確認します。変更内容は状況によって異なります。

    Exercise 2 の冒頭で確認したカスタム指示ファイルに基づき、TSDoc ドキュメントコメント、TypeScript 規約、コメントヘッダーが適用されているかを確認します。

6. 更新内容を確認して問題がなければ、**Conversation** タブに戻り、下にスクロールします。
7. ワークフローの承認待ちを示す表示を確認します。
8. 承認待ちのワークフローがあれば、**Approve and run workflows** を選択します。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. プルリクエストのチェック欄にワークフローが登録されます。問題がなければ、単一 Astro アプリのプロジェクトチェックが成功します。完了まで数分かかる場合があります。

## GitHub Copilot に変更を依頼する

Copilot とのプルリクエスト作業は一方通行ではありません。チームメンバーと同じように、プルリクエストやコードのインラインコメントで Copilot にメンションできます。Copilot はコメントを確認し、対応するセッションを新たに開始します。結果は確定的ではないため、依頼内容を一律に指定することはできません。たとえば次のような変更を依頼できます。

- 各コードファイルの先頭に、そのファイルの内容を簡潔に説明するコメントヘッダーを追加する。
- TypeScript ファイルと Astro ファイルに TSDoc ドキュメントコメントを追加する。
- Astro アプリの構成を説明する README を作成する。

1. 上記の案や、コードベースのドキュメントに関する別の提案を使い、**@copilot** にメンションして生成されたドキュメントの変更を依頼するコメントを追加します。
2. **View Session** を選択し、Copilot の作業を確認します。Copilot が変更のために新しいセッションを開始することを確認します。
3. **Back to pull request** を選択すると、プルリクエストに戻れます。

    ![プルリクエストに戻る](../../../_images/ex7-back-to-pr.png)

4. Copilot が変更を完了すると、プルリクエストに新しいコミットが表示されます。
5. **Files changed** タブを選択して変更をレビューします。

納得できるまで反復して改善できます。準備ができたら、ドラフト PR をレビュー可能な状態に変更し、`main` ブランチにマージします。

![PR をレビュー可能な状態に変更](../../../_images/ex7-ready-for-review.png)

## 関連ゲーム機能をレビューする

ゲーム詳細ページに関連ゲームを表示する Issue を解決するために、Copilot が作成した PR に戻ります。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Show related games on the game details page** などのタイトルが付いた PR を選択します。
4. **Files changed** タブを選択して、生成されたコードをレビューします。
5. 更新内容を確認して問題がなければ、**Conversation** タブに戻り、下にスクロールします。
6. ワークフローの承認待ちを示す表示を確認します。
7. 承認待ちのワークフローがあれば、**Approve and run workflows** を選択します。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

8. プルリクエストのチェック欄にワークフローが登録されます。問題がなければ、単一 Astro アプリのプロジェクトチェックが成功します。完了まで数分かかる場合があります。
9. **省略可能:** Codespace でこのブランチに切り替え、関連ゲーム機能を手動でテストすることもできます。Codespace に移動してターミナルを開き、Copilot が作成したブランチ名 (例: **copilot/fix-8**) に `<branch-name>` を置き換えて、次のコマンドを実行します。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

Copilot が関連ゲーム機能を作成しました。前と同様に、Copilot cloud agent に更新を依頼して反復できます。たとえば、表示する関連ゲームの数の調整や、コメントヘッダーと TSDoc ドキュメントコメントの追加を依頼できます (この Issue を割り当てたのはカスタム指示を更新する前だったことに注意してください)。**Conversation** タブでコメントを追加すると、Copilot が確認して新しいセッションを開始します。

## アクセシビリティ機能をレビューする

最後に、アクセシビリティ用カスタムエージェントで実装した機能をレビューします。この PR には、Exercise 3 で依頼したハイコントラストモードと、Exercise 4 の mission control で追加を依頼したライトモードの両方が含まれているはずです。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Add high contrast mode to website** などのタイトルが付いた PR を選択します。

> [!NOTE]
> Copilot がまだ作業中の場合、プルリクエストのタイトルに **[WIP]** が表示されます。その場合は作業が終わるまで待ちます。数分かかることがあります。

4. **Files changed** タブを選択して、生成されたコードをレビューします。
5. 特に次の点に注意して実装を確認します。
   - モードを切り替える UI コンポーネント。
   - ユーザー設定を保持するための local storage の使用。
   - ハイコントラストモードとライトモードの CSS またはスタイルの変更。
   - アクセシビリティ属性 (ARIA ラベル、キーボード操作など)。
   - モードの切り替えを管理する JavaScript/TypeScript コード。

6. 更新内容を確認して問題がなければ、**Conversation** タブに戻り、下にスクロールします。
7. ワークフローの承認待ちを示す表示を確認します。
8. 承認待ちのワークフローがあれば、**Approve and run workflows** を選択します。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. プルリクエストのチェック欄にワークフローが登録されます。問題がなければ、単一 Astro アプリのプロジェクトチェックが成功します。完了まで数分かかる場合があります。
10. **省略可能:** Codespace でこのブランチに切り替えて、アクセシビリティ機能を手動でテストできます。Codespace に移動してターミナルを開き、Copilot が作成したブランチ名に `<branch-name>` を置き換えて、次のコマンドを実行します。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    次にアプリケーションを起動し、ブラウザーでハイコントラストモードとライトモードの切り替えが動作すること、ページを再読み込みしても設定が保持されることを確認します。

アクセシビリティ用カスタムエージェントが、アクセシビリティのベストプラクティスに従って Copilot の実装を支援したことを確認します。アクセシビリティ上の懸念や改善点があれば、前の PR と同じように **@copilot** にメンションして変更を依頼できます。

## 省略可能な演習 — さらにタスクを委任する

cloud agent は、実際のバックログ項目を割り当てて別の作業に集中するときに役立ちます。習慣にするため、リポジトリにいくつか Issue を登録して Copilot に割り当ててみましょう。例:

- ゲーム詳細ページに支援者向けの関心表明フォームを作成する。
- ゲーム一覧ページにページネーションを実装する。
- データアクセスヘルパーに入力検証とエラー処理を追加する。

## まとめ

Cloud agent のハーネスを完了しました。各レッスンを通じて、次の作業を行いました。

- **このリポジトリに含まれるカスタム指示ファイルを確認し**、後で cloud agent の出力に与える影響を把握しました。
- **Issue を Copilot cloud agent に割り当て**、環境のセットアップ、計画、非同期の実行を確認しました。
- アクセシビリティ用の **カスタムエージェントを作成して使用し**、ハイコントラストとライトモードの切り替えを追加しました。
- **Copilot エージェントページを mission control として使い**、アクセシビリティのセッションを監視し、作業中に方向付けました。
- Copilot のプルリクエストを**レビューして反復的に改善し**、`@copilot` に変更を依頼し、ワークフローを承認しました。

## 振り返りと次のステップ

Cloud agent のハーネスを完了しました。さらに学習を続ける場合は、別の環境向けハーネスも組み合わせて活用できます。

- 🖥️ **[VS Code ハーネス](../../vscode/)** — IDE で Copilot Chat のエージェントモードと MCP 統合を試します。
- 💻 **[CLI ハーネス](../../cli/)** — Plan と Autopilot モード、agent skill、QA カスタムエージェント、Playwright MCP、Agent Merge、cloud agent に戻る任意の `/delegate` 機能を使って、レビュー済みの変更を 2 件リリースします。

自分のリポジトリでは、次のような作業を試せます。

- リファクタリングやテストカバレッジに関する Issue を cloud agent に割り当てる。
- 別の分野に特化したカスタムエージェントを作成する。
- 異なる技術スタック向けに `copilot-setup-steps.yml` を設定する。

## リソース

- [GitHub Copilot][github-copilot]
- [Copilot エージェントについて][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent のセットアップワークフローのベストプラクティス][cloud-agent-best-practices]
- [Copilot cloud agent のファイアウォールを設定する][agent-firewall]

---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall
[previous-lesson]: ../4-managing-agents/
