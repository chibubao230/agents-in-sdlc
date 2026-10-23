---
title: "演習 1 - カスタム指示 (VS Code)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: エージェントモード →][next-lesson]

生成 AI を使ううえで、コンテキストは重要です。タスクの進め方に決まりがある場合は、そのガイダンスを Copilot が利用できるようにします。Copilot に特定のコンテキストを伝える方法はいくつかあります。重要な方法の 1 つが[指示ファイル][instruction-files]です。コード生成に関するガイダンスを提供します。

この演習では、次の内容を学習します。

- リポジトリのカスタム指示やパスに応じた指示ファイルを通じて、プロジェクト固有のコンテキスト、コーディングガイドライン、ドキュメント標準が Copilot に伝わる仕組みを確認します。
- 現在の指示を使って、フィルター機能の最初のデータスライス (publisher ヘルパー) を生成します。
- `.github/copilot-instructions.md` にリポジトリ全体に適用する標準を追加します。
- 同じプロンプトをもう一度実行し、生成コードに新しい標準が反映されることを確認します。
- 次の演習で Copilot が更新済みのガイダンスを使えるよう、指示の更新とフィルター機能の一部を `main` にコミットします。

> [!CAUTION]
> 生成コードが設定した標準から外れる場合があります。Copilot の動作は確率的です。この演習では、指示の更新後にコードが一字一句同じになることではなく、動作の傾向が変化することを確認します。

## 指示ファイル

### シナリオ

Tailspin Toys には、開発方法に関するガイドラインと要件があります。

- データ層には必ずユニットテストを用意します。
- UI はダークモードにし、モダンな印象にします。
- コードには TSDoc のドキュメントコメントを追加します。
- 各ファイルの先頭に、そのファイルの目的を説明するコメントブロックを追加します。

指示ファイルを使って、これらの方針に沿った作業に必要な情報を Copilot に伝えます。

### カスタム指示

カスタム指示を使うと、コーディングスタイルや要件に関するコンテキストと設定を Copilot に伝え、より適切な提案やコードスニペットを得られます。推奨する規約、ライブラリ、コードコメントの形式などを指定できます。リポジトリ全体、または特定の種類のファイルを対象に設定できます。

指示ファイルには 2 種類あります。

- `.github/copilot-instructions.md` は、リポジトリへの**すべての**リクエストで Copilot に送信される単一の指示ファイルです。ほとんどのリクエストに関係するプロジェクトレベルの情報を記載します。
- `.github/instructions/*.instructions.md` は、特定の言語、ファイル形式、タスクに関するガイドラインを記載します。

> [!NOTE]
> ほかの指示ファイル形式への対応状況は、利用する環境によって異なります。特定の形式に依存する前に、[カスタム指示の対応状況][custom-instructions-support]を確認してください。

[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## このプロジェクトのカスタム指示ファイルを確認する

スタータープロジェクトには、すでにいくつかの指示ファイルが含まれています。変更による効果を確認するため、まず現在のファイルを見てみましょう。

1. codespace に戻ります。
2. ターミナルではなく Codespaces エディターで `.github/copilot-instructions.md` を開きます。
3. プロジェクトの概要とコーディングに関するガイダンスを確認します。この指示は、このリポジトリでの Copilot とのすべてのやり取りに適用されます。
4. `.github/instructions` フォルダーを開き、ファイルを確認します。Astro ファイル、Drizzle のデータ層、テストなどに関する指示があります。
5. `.github/instructions/unit-tests.instructions.md` を開き、先頭の `applyTo` フィールドを確認します。この glob によって適用対象のファイルが決まります。
6. `.github/instructions/drizzle.instructions.md` を開き、他の指示ファイルや既存のプロジェクトファイルへのリンクを確認します。大きな指示を再利用できる小さなファイルに分割し、コード生成時に参照する例を Copilot に示せます。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションにはコーディング標準が記載されていますが、コード内ドキュメントの要件はまだありません。次の手順で、TSDoc コメントとファイルヘッダーのルールを追加します。

## 変更用のブランチを作成する

ベストプラクティスに従って、変更用のブランチを作成します。

1. 前の演習で使った codespace に戻ります。
2. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、新しいターミナルを開きます。
3. 次のコマンドで新しいブランチを作成し、切り替えます。

   ```bash
   git checkout -b custom-instructions
   ```

## 指示を更新する前に Copilot Chat を使う

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に、codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を開始します。モードとモデルの選択は演習ごとに異なります。必要な箇所で説明します。

カスタム指示の効果を確認するため、最初に現在の指示ファイルを使ってプロンプトを送信します。その後、指示を更新して同じプロンプトをもう一度送信し、違いを確認します。

1. Copilot が必要なコンテキストだけを使うよう、前の演習で開いたエディタータブを閉じます。
2. `src/lib/publishers.ts` を開き、ヘルパーの作成場所を Copilot に示します。
3. Chat ビューのエージェントのドロップダウンで **Agent** を選択し、Copilot がファイルを変更できるようにします。

   ![Chat ビューのエージェント選択メニューを示すスクリーンショット](../../../_images/shared-chat-mode-selector.png)

4. 次のプロンプトを送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot がプロジェクトを調査し、コードを更新します。通常、ヘルパーファイルとテストにまたがる変更が行われます。
6. 提案されたヘルパーが型付けされた関数であり、最初の引数に `db` クライアントを受け取り、型付けされた publisher の配列を返すことを確認します。これは `src/lib/*.ts` に適用される `.github/instructions/drizzle.instructions.md` のデータ層規約によるものです。
7. 提案されたコードに TSDoc ドキュメントコメントとファイルレベルのコメントヘッダーが**ない**ことを確認します。

> [!CAUTION]
> Copilot は確率的に動作するため、指示がなくてもドキュメントコメントを追加する場合があります。その場合も問題ありません。この演習では、指示の更新後に動作が一貫して改善することを確認します。

## チームのガイダンスに合わせて指示ファイルを更新する

既存のファイルはよい出発点ですが、まだ不足があります。`copilot-instructions.md` を変更して、新しく生成する TypeScript に TSDoc コメントを追加するよう指示します。

1. `.github/copilot-instructions.md` で **Code formatting guidance** セクションを探します。35 行目付近にあります。
2. 末尾の箇条書きとして次の内容を追加します。

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

ファイルは自動的に保存されます。

> [!TIP]
> 前のレッスンで確認したように、リポジトリ全体の指示は `.github/copilot-instructions.md` に、言語、ファイル形式、タスクに特化した指示は `*.instructions.md` に記載できます。ドキュメントコメントのルールのようなプロジェクト全体の標準には、リポジトリレベルのファイルが適しています。

## プロンプトをもう一度送信して変更を確認する

1. Copilot Chat に戻り、**New Chat** を選択して会話を新しくします。
2. `src/lib/publishers.ts` をもう一度開き、Copilot が適切なファイルに注目するようにします。
3. 前と**同じプロンプト**を送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. 提案されたファイルの先頭に、次のようなコメントブロックがあることを確認します。

   ```typescript
   /**
    * Tailspin Toys クラウドファンディングプラットフォーム用の publisher データアクセスヘルパーです。
    * データベースから publisher 情報を取得する関数を提供します。
    */
   ```

5. 提案された関数に、次のような TSDoc コメントが含まれていることを確認します。

   ```typescript
   /**
    * すべての publisher の ID と名前の一覧を返します。
    *
    * @param db - Drizzle データベースクライアント。
    * @returns publisher オブジェクトの配列に解決される Promise。
    */
   ```

Copilot に新しいプロジェクト標準に従うよう指示し、次の演習で使用する実際のコードに適用しました。

## 指示ファイルをコミットしてブランチをプッシュする

指示ファイルもリポジトリ内の他のファイルと同様に、ソース管理で扱います。指示の更新とヘルパーの変更をコミットし、ブランチをリポジトリにプッシュします。

1. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、新しいターミナルを開きます。
2. ターミナルで次のコマンドを実行し、指示の更新とヘルパーの変更があることを確認します。

   ```bash
   git status
   ```

3. ターミナルで次のコマンドを実行して、指示の更新と Copilot がヘルパーの基礎として変更したファイルをステージしてコミットします。

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # git status にテストなどの追加の変更が表示された場合は、それらのファイルも追加します。
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. 次のコマンドでブランチをリポジトリにプッシュします。

   ```bash
   git push -u origin custom-instructions
   ```

## プルリクエストを作成してマージする

ブランチをプッシュしたら、バックログのドキュメント標準に関する Issue と関連付けてプルリクエストを作成します。手作業で行うこともできますが、プロジェクトに接続されている GitHub ツールを使って Copilot に依頼できます。Copilot に Issue を探し、Issue を完了する PR を作成し、マージするようプロンプトを送信します。

> [!NOTE]
> この演習では、Copilot に GitHub 上の操作を依頼するのは初めてです。プロジェクトテンプレートには GitHub ツールがあらかじめ接続されているため、設定は不要です。Copilot が初めてツールを使うときに、VS Code から GitHub へのサインインを求められる場合があります。画面の指示に従って許可してください。この接続方法 (Model Context Protocol) は後の演習で学習します。

1. codespace で Copilot Chat を開きます。
2. <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd> (Windows/Linux) を押して Copilot Chat ビューを開き、エージェントの選択メニューで **Agent** が選択されていることを確認します。
3. 現在のブランチから PR を作成し、指示の更新と新しい publishers ヘルパーの基礎を説明して、該当する Issue を見つけるよう Copilot に依頼します。

   ```plaintext
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Copilot に代わって GitHub 操作を実行する **Allow** の確認が表示されたら、コマンドを確認して適切に許可します。
5. PR が作成されたら、Copilot に PR をマージしてローカルブランチを `main` に戻すよう依頼します。

   ```plaintext
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

6. Copilot に代わって GitHub 操作とシェルコマンドを実行する **Allow** の確認が表示されたら、コマンドを確認して適切に許可します。

GitHub Copilot の支援を受けて、プルリクエストを作成してマージしました。

## まとめと次のステップ

Copilot がこのプロジェクトの指示ファイルからコンテキストを得る仕組みを確認し、VS Code の Copilot Chat を使って次の作業を行いました。

- 既存の指示を使ってコード生成プロンプトを送信し、生成結果を確認しました。
- `.github/copilot-instructions.md` にリポジトリ全体に適用する標準を追加しました。
- 同じプロンプトをもう一度送信し、提案されたコードに新しい標準が反映されることを確認しました。
- 次の演習で使えるよう、指示の更新とヘルパーの基礎を `main` にコミットしました。

次に、[エージェントモード][next-lesson]で指示を活用し、コードベース全体に新機能を追加します。

## リソース

- [Copilot CLI にカスタム指示を追加する][instruction-files]
- [カスタム指示の対応状況][custom-instructions-support]
- [カスタム指示作成のベストプラクティス][instructions-best-practices]
- [Awesome Copilot — 指示ファイルなどのリソース集][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
