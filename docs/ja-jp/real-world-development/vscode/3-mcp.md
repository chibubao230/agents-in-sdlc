---
title: "演習 3 - Playwright MCP サーバーで機能をテストする"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: エージェントモード][previous-lesson] · [次のレッスン: カスタムエージェント →][next-lesson]

エージェントモードを使ってフィルター機能を作成しました。プルリクエストを開く前に、ブラウザーで実際に動作することを確認します。自分でアプリを操作する代わりに、**Playwright MCP サーバー**を接続して Copilot に実際のブラウザーを操作してもらい、機能をテストします。その後、ブランチを公開して PR を開きます。

この演習では、次の内容を学習します。

- Model Context Protocol (MCP) とは何か、MCP サーバーが Copilot にツールを追加する仕組みを理解します。
- Playwright MCP サーバーをワークスペースに追加します。
- Copilot にブラウザーでフィルター機能を手動テストするよう依頼します。
- ブランチを公開して、フィルター作業のプルリクエストを作成します。

## Model Context Protocol (MCP) とは

エージェントモードは、エディターの外にアクセスできるとさらに強力になります。Copilot がそれを実現する方法が Model Context Protocol (MCP) です。エージェントが外部のツールやサービスと通信するための標準です。

![コンテキスト、LLM、ツール、および MCP サーバーと VS Code 拡張機能が提供するツールとの連携を示すエージェントモードの仕組みの図](../../../_images/ex1-mcp-diagram.png)

[Model Context Protocol (MCP)](https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/) は、AI エージェントが外部のツールやサービスと通信するための仕組みです。MCP を使うと、AI エージェントは外部ツールやサービスとリアルタイムに通信できます。最新情報にアクセスしたり (resources)、ユーザーに代わって操作したり (tools) できます。

これらのツールやリソースには、AI エージェントと外部ツールやサービスをつなぐ MCP サーバーを介してアクセスします。MCP サーバーは、AI エージェントと外部ツール (既存の API や npm パッケージなどのローカルツール) との通信を管理します。各 MCP サーバーは、AI エージェントが利用できる異なるツールやリソースのセットを提供します。

よく利用されている MCP サーバーの例:

- **[GitHub MCP Server](https://github.com/github/github-mcp-server)**: GitHub リポジトリを管理する API を利用できます。リポジトリの作成や更新、Issue とプルリクエストの管理などを AI エージェントが実行できます。
- **[Playwright MCP Server](https://github.com/microsoft/playwright-mcp)**: Playwright を使ったブラウザー自動化機能を提供します。Web ページへの移動、フォーム入力、ボタン選択などを AI エージェントが実行できます。

ほかにも、さまざまなツールやリソースを利用できる MCP サーバーがあります。GitHub は、エコシステムの検索性と貢献を高めるため、[MCP レジストリ](https://github.com/mcp)をホストしています。

> [!CAUTION]
> セキュリティについては、MCP サーバーをプロジェクト内のほかの依存関係と同じように扱ってください。利用前にソースコードを確認し、発行元を検証して、セキュリティへの影響を検討します。信頼できる MCP サーバーのみを使い、機密リソースや操作へのアクセス許可には注意してください。

## MCP の構成を確認する

`.vscode/mcp.json` ファイルで、この VS Code ワークスペースから使える MCP サーバーを設定します。

1. codespace で `.vscode/mcp.json` を開きます。
2. すでに設定されている `github` サーバーを確認します。

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        }
      }
    }
    ```

この `github` エントリはプロジェクトテンプレートに含まれています。そのため、前の演習で Copilot が Issue のバックログを読み取れました。[リモート GitHub MCP サーバー][remote-github-mcp-server]を使うため、ローカルにインストールする必要はありません。VS Code は HTTP 経由で接続し、Copilot がツールを初めて使うときに GitHub で認証します。

## Playwright MCP サーバーを追加する

次に 2 つ目のサーバーを追加します。[Playwright MCP サーバー][playwright-mcp-server]を使うと、Copilot が操作できるブラウザーを利用できます。機能のテストに役立ちます。

1. `.vscode/mcp.json` に `github` と並べて `playwright` エントリを追加し、ファイルを次のようにします。

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        },
        "playwright": {
          "command": "npx",
          "args": ["@playwright/mcp@latest", "--headless"]
        }
      }
    }
    ```

2. ファイルを保存します。

リモートの `github` サーバーとは異なり、`playwright` は**ローカル**サーバーです。VS Code は `npx @playwright/mcp@latest` を実行して起動します。`--headless` フラグを指定すると、Playwright は画面に表示しないブラウザーを実行します。画面を表示するデスクトップがない codespace では、この設定が必要です。

> [!NOTE]
> Tailspin Toys プロジェクトではエンドツーエンドテストに Playwright をすでに使っているため、通常は必要なブラウザーもインストール済みです。Copilot がブラウザーがないと報告した場合は、`npx playwright install chromium` を実行するよう依頼してから再試行します。

## サーバーを起動して信頼する

VS Code は MCP サーバーを**必要に応じて**起動します。Copilot がサーバーのツールを初めて使うときに VS Code がサーバーを起動し、信頼するかどうかを確認します。

1. `.vscode/mcp.json` の `playwright` エントリの上に **Start** 操作が表示されます。選択するとサーバーが起動し、構成を確認できます。
2. VS Code がサーバーを信頼するか確認する場合は、構成を確認し、サーバーを起動するために信頼することを選択します。

手動での起動は省略できます。省略した場合は、次の手順で Copilot がツールを初めて使うときに VS Code がサーバーを起動し、信頼するか確認します。

## フィルター機能をテストする

[Playwright MCP サーバー][playwright-mcp-server]は Copilot が操作できる実際のブラウザーを提供します。自分でアプリを操作する代わりに、エージェントがページを開き、移動し、フィルターを適用し、結果を確認して、概要を報告します。会話を続けたまま機能の動作を確認できます。

Playwright MCP サーバーは、スクリーンショットではなくページの[アクセシビリティツリー][playwright-mcp-server]を参照します。つまり、エージェントは支援技術と同じように、ラベル付きの構造化要素 (ボタン、リンク、リスト項目) に基づいて処理します。そのため、簡単な機能テストがアクセシビリティの基本確認にもなります。

サーバーを接続してアプリを実行した状態で、次のプロンプトを Copilot に送信し、作成したフィルター機能を試します。

```text
Using the Playwright MCP server, open a browser to the running app at http://localhost:4321 and verify the new game filtering feature:

1. Go to the games page and note how many games are listed.
2. Apply a category filter and confirm the list updates to only show games in that category.
3. Clear it, then apply a publisher filter and confirm the list updates to that publisher.
4. Combine a category and a publisher filter and confirm the results respect both.

Report what you observe at each step, and call out anything that does not behave as expected.
```

Copilot は Playwright MCP サーバーを通じてブラウザーを起動し、各手順を実行して結果を報告します。報告を Issue の受け入れ基準と照らし合わせます。期待どおりに動作しない場合は、追加の質問をしたり、プルリクエストを開く前にコードを修正するよう依頼したりします。

> [!NOTE]
> このテストでは、アプリが `http://localhost:4321` で実行されている必要があります。開発サーバーを停止した場合は、プロンプトを送信する前に再起動します。Copilot が Playwright MCP サーバーを初めて使うときにブラウザーのダウンロードが必要な場合があります。ブラウザーがないと報告されたら `npx playwright install chromium` を実行するよう依頼し、もう一度試します。

[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp

## ブランチを公開してプルリクエストを作成する

機能の動作を確認できたので、チームがレビューできるようにプルリクエスト (PR) を作成します。最初に `filtering-vscode` ブランチを公開します。

1. codespace で **Source Control** パネルを開き、Copilot が行った変更を確認します。
2. **+** アイコンを選択して変更をステージします。
3. **Sparkle** ボタンでコミットメッセージを生成します。

   ![変更内容を表示する Source Control パネルのスクリーンショット](../../../_images/ex3-source-control-changes.png)

4. **Publish** を選択してブランチをリポジトリにプッシュします。

## プルリクエストを作成する

プルリクエストは github.com や GitHub コマンドラインインターフェイス (CLI) など、複数の方法で作成できます。GitHub Copilot を使っているので、PR 作成も依頼しましょう。Copilot が関連 Issue を見つけ、Issue と関連付けた PR を作成できます。

1. Copilot Chat パネルで **New Chat** を選択し、新しいセッションを開始します。
2. エージェントのドロップダウンで **Agent** が選択されていることを確認し、Copilot が GitHub ツールを使えるようにします。
3. 次のプロンプトを Copilot に送信し、PR を作成します。

   ```plaintext
   Find the issue in the repo related to filtering by category and publisher. Create a new pull request for the current branch, and associate it with the correct issue.
   ```

4. 必要に応じて **Continue** を選択し、情報の取得や操作を Copilot に許可します。GitHub ツールを初めて使うときに GitHub の認証を求められる場合は、画面の指示に従って許可します。
5. Copilot が Issue を検索し、該当するものを見つけて PR を作成することを確認します。
6. Copilot が作成したリンクからプルリクエストを開き、内容をレビューします。ただし、**まだマージしないでください**。

## まとめと次のステップ

この演習では、次の内容を学習しました。

- Model Context Protocol (MCP) と、MCP サーバーが Copilot にツールを追加する仕組みを確認しました。
- Playwright MCP サーバーをワークスペースに追加しました。
- Playwright MCP を使って、リリース前にブラウザーでフィルター機能を手動テストしました。
- ブランチを公開し、フィルター機能のプルリクエストを作成しました。

MCP サーバーを使って機能をテストしましたが、MCP は Copilot を拡張する方法の 1 つにすぎません。次に、アクセシビリティレビューなど、対象を絞ったタスクを効率化する[カスタムエージェントを作成します][next-lesson]。

## リソース

- [MCP とは何か、なぜ話題なのか][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [GitHub MCP Server][github-mcp-server]
- [GitHub MCP Registry][mcp-registry]
- [VS Code の MCP サーバー][vscode-mcp-config]

[previous-lesson]: ../2-agent-mode/
[next-lesson]: ../4-custom-agents/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[github-mcp-server]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[remote-github-mcp-server]: https://github.blog/changelog/2025-06-12-remote-github-mcp-server-is-now-available-in-public-preview/
[vscode-mcp-config]: https://code.visualstudio.com/docs/agents/reference/mcp-configuration
