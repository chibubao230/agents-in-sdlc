---
title: "演習 2 - Copilot エージェントモードで機能を追加する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

アプリケーションへの小さな変更でも、通常は複数のファイルを更新し、テストを実行する必要があります。開発者は必要なファイルを探して変更し、テストを実行し、デバッグし、変更し忘れたファイルを見つけて、さらに更新します。こうした作業は続いていきます。

このような作業に役立つのが Copilot エージェントモードです。

Copilot エージェントモードは、IDE 内でより自律的に動作します。開発者と同じように、まず既存のプロジェクト構成を調査し、必要な更新を行い、テストなどを実行し、問題が見つかれば自動的に修正します。エージェントモードを使ってサイトに機能を追加する方法を確認しましょう。

> [!NOTE]
> 名前は似ていますが、エージェントモードと cloud agent は異なる体験を提供します。エージェントモードは IDE 内で作業するため、すばやくフィードバックを得て、やり取りできます。cloud agent はチームメンバーのように非同期で作業し、Issue とプルリクエストを通じてやり取りします。

この演習では、次の内容を学習します。

- Copilot エージェントモードがプロジェクトを調査し、関連ファイルを特定して、複数ファイルにわたる変更を行う方法。
- GitHub Copilot エージェントモードで UI とデータ層にまたがる機能を実装する方法。
- Copilot エージェントモードが生成した変更とテストをレビューしてから、コードベースにマージする方法。

## シナリオ

ゲームの一覧が増えてきたため、ユーザーがカテゴリと発行元で絞り込めるようにします。前の演習で発行元ヘルパーを追加したので、残りのデータ層、UI、テストを Copilot エージェントモードで完成させます。

## Tailspin Toys の Web サイトを実行する

変更を加える前に、Tailspin Toys の Web サイトを確認し、現在の機能を把握しましょう。

この Web サイトは、開発者をテーマにしたボードゲームのクラウドファンディングプラットフォームです。ゲームの一覧と詳細を表示します。Astro アプリはビルド時にページを静的 HTML としてレンダリングします。ページは Drizzle ORM を使ってフロントマターからローカル SQLite データベースを直接参照します。別のバックエンド API やクライアント側 UI フレームワークはありません。再利用可能なデータアクセスヘルパーは `src/lib/` にあり、インタラクティブな動作は標準 DOM API を使う小さなスコープ付き Astro `<script>` で追加します。

### Web サイトを起動する

単一の Astro 開発サーバーを起動する `npm run dev` タスクが用意されています。GitHub Codespace で次の手順を実行します。

1. codespace に戻ります。前の演習の後、`main` にいるはずです。
2. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、Codespace 内で新しいターミナルを開きます。
3. フィルター作業用のブランチを作成して切り替えます。

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 次のコマンドで Web サイトを起動します。

   ```bash
   npm run dev
   ```

   Astro 開発サーバーの準備ができると、次のような URL が表示されます。

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> `http://localhost:4321` を開くためのブラウザーウィンドウを表示するダイアログが開いた場合は、**x** を選択して閉じます。

5. <kbd>Command</kbd> (Mac) または <kbd>Ctrl</kbd> (Windows/Linux) を押しながら、ターミナルに表示された Astro サーバーのアドレス `http://localhost:4321` を選択して、Web サイトを開きます。

> [!NOTE]
> codespace では、ターミナルに表示された localhost URL を選択すると、自動的に `https://<your-codespace-name>-4321.app.github.dev/` にリダイレクトされます。これは Web サーバーをホストする codespace へのプライベートトンネルです。

### Web サイトを確認する

Web サイトが起動したら、機能を確認できます。

- **ホームページ**: ボードゲームのタイトル、画像、説明の一覧が表示されます。
- **ゲーム詳細ページ**: ゲームを選択すると、タイトル、説明、発行元、カテゴリなどの情報が表示されます。

## Copilot でバックログを確認する

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に、codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を開始します。モードとモデルの選択は演習ごとに異なります。必要な箇所で手順を説明します。

Web サイトの初期実装は動作しますが、機能を追加して改善しましょう。まずバックログを確認します。テンプレートからプロジェクトをセットアップすると、Issue のバックログも自動的に作成されます。GitHub Copilot に項目を表示してもらいます。

1. Chat ビューのエージェントのドロップダウンから **Agent** を選択します。**Agent** は複数ファイルにわたる変更を自律的に計画して実装し、ターミナルコマンドを実行してツールを呼び出します。

   ![Chat ビューのエージェント選択メニューのスクリーンショット](../../../_images/shared-chat-mode-selector.png)

2. 利用可能なモデルの一覧から **Claude Sonnet 4.5** を選択します。

> [!CAUTION]
> このワークショップの作成者が特定のモデルを推奨しているわけではありません。作成時に Claude Sonnet 4.5 を使ったため、体験をなるべく一貫させる目的で手順に含めています。ただし、LLM の出力は確率的なため、手順と異なる提案が表示される場合があります。これは正常であり、想定された動作です。

> [!NOTE]
> LLM の確率的な性質により、Copilot が別の MCP コマンドを使う場合がありますが、タスクは完了できます。

3. 次のプロンプトを Copilot に送信し、バックログの Issue について質問します。

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. **Continue** を選択して、すべての Issue を一覧表示するコマンドを実行します。
5. 生成された Issue の一覧を確認します。

Copilot がユーザーにとって最も役立つと考える項目に基づいて、優先順位も付けたことを確認します。

## 指示ファイルを確認する

Copilot にコードを生成してもらう前に、作業のコンテキストを伝える指示ファイルを確認します。Web サイトに機能を追加する方法が記載された[ユーザーインターフェイス (UI) 指示ファイル](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md)を使います。

1. codespace で `.github/instructions/ui.instructions.md` を開きます。
2. 機能追加の全般的なガイダンスを確認します。次の内容が含まれます。
   - アーキテクチャの概要。
   - コンポーネント設計、テスト容易性、アクセシビリティの原則。
   - Astro、Tailwind CSS、Drizzle など、ファイル形式ごとの指示へのリンク。

> [!TIP]
> 指示ファイルから、ほかの指示ファイルやプロジェクト内のファイルを参照できます。パスは指示ファイルからの相対パスです。指示を再利用したり、複雑な指示を小さな単位に分割したり、例やテンプレートを示したりできます。

## フィルター機能を実装する

フィルターを完成させるには、少なくとも次の 3 つの変更が必要です。

- データ層 (`src/lib/`) の残りのフィルターロジックを追加または改善する。
- フィルターの動作をテストするテストを追加または更新する。
- ゲーム一覧ページにフィルター UI を追加する。

コードベースにマージする前に、テストを実行して成功させる必要もあります。Copilot エージェントモードにこれらの作業を依頼できます。

1. 現在の Copilot 会話を続けるか、**New Chat** を選択して新しい会話を始めます。
2. **Add Context**、**Instructions**、**ui** の順に選択して、指示ファイルを追加します。

   ![UI 指示ファイルを選択する例のスクリーンショット](../../../_images/ex3-select-instructions-file.png)

3. Chat ビューのエージェントのドロップダウンで **Agent** が選択されていることを確認します。

   ![Chat ビューのエージェント選択メニューのスクリーンショット](../../../_images/shared-chat-mode-selector.png)

4. モデルに **Claude Sonnet 4.5** が選択されていることを確認します。
5. バックログの関連 Issue の要件に基づいて実装するよう、次のプロンプトを Copilot に送信します。

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Copilot がプロジェクトを調査し、機能に関連するファイルを探します。データ層ヘルパー、UI、テストが見つかり、ファイルの変更とテストの実行が始まることを確認します。

   ![Copilot がプロジェクトファイルを調査する様子を示すスクリーンショット](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot はプロジェクトの調査、ファイルの変更、テストの実行など、複数の作業を行います。タスクやコードベースの複雑さによっては数分かかります。作業中にコードエディターに **Keep** と **Undo** が表示されることがあります。Copilot が完了するとすべての変更に **Keep** または **Undo** が表示されるため、作業中に選択する必要はありません。

7. Copilot の確認に応じて **Continue** を選択し、テストを実行します。

   ![Copilot Chat パネルでテストの実行確認を求めるダイアログのスクリーンショット](../../../_images/ex3-agent-mode-run-tests.png)

8. 作業中に一時停止したり、テストが失敗したりする場合があります。問題ありません。Copilot はエラーが検出されなくなるまで、コード生成とテストを繰り返します。

   ![Copilot エージェントモードの完了した Chat セッションのスクリーンショット](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 生成されたコードに問題がないか確認します。

> [!CAUTION]
> Copilot やその他の AI ツールが生成したコードは、必ずレビューしてください。

10. Web サイトを開いているブラウザーに戻り、新しい機能を試します。
11. 動作を確認してコードをレビューした後、Copilot Chat ウィンドウで **Keep** を選択します。

## まとめと次のステップ

GitHub Copilot エージェントモードを使って、Tailspin Toys Web サイトに機能を追加する方法を学びました。

- GitHub Copilot エージェントモードを使って、UI とデータ層にまたがる機能を実装しました。
- Copilot エージェントモードがプロジェクトを調査し、関連ファイルを特定して、複数ファイルにわたる変更を行う様子を確認しました。
- Copilot エージェントモードが生成した変更とテストをレビューしてから、コードベースにマージする方法を学びました。

次に、[Playwright MCP サーバーで機能をテストし][next-lesson]、プルリクエストを作成します。

### 追加の演習 — ページネーションを実装する

ゲーム一覧が増えるとページネーションが必要になります。この演習で学んだことを使い、Copilot にページネーションを依頼してみましょう。コードを考える際は、次の点を検討します。

- 既存の指示ファイルを使うなど、既存のベストプラクティスに従います。
- ユーザーがページサイズを選べるようにするか、固定値にするかなど、ページネーションの設計を検討します。
- 希望する実装に必要なガイダンスをプロンプトに含めます。
- Copilot に変更を依頼してコンテキストを与え、反復が必要になる場合があります。これは Copilot を使う際の通常の流れです。

## リソース

- [Copilot の Ask、Edit、Agent モード: それぞれの機能と使いどころ][choose-mode]
- [VS Code のエージェントモード][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
