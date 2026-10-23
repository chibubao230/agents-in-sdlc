---
title: "演習 1 - カスタム指示 (Cloud agent)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: Copilot cloud agent →][next-lesson]

生成 AI を使ううえで、コンテキストは重要です。タスクの進め方に決まりがある場合や、Copilot が知っておくべき背景情報がある場合は、その情報にアクセスできるようにします。[指示ファイル][instruction-files]を使うと、何をするかだけでなく、どのように行うかも Copilot に伝えられます。

この演習では、次の内容を学習します。

- リポジトリのカスタム指示やパスに応じた指示ファイルを通じて、プロジェクト固有のコンテキスト、コーディングガイドライン、ドキュメント標準が Copilot に伝わる仕組みを確認します。
- `.github/copilot-instructions.md` にリポジトリ全体に適用する標準を追加します。

> [!NOTE]
> VS Code や CLI のハーネスとは異なり、ここでは変更前後のプロンプトを実行しません。Copilot cloud agent は GitHub Issue 上で非同期に動作するため、効果をリアルタイムに比較するのは難しいためです。後の演習で cloud agent が作成したプルリクエストをレビューするときに、指示ファイルの影響を確認します。

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

- `.github/copilot-instructions.md` は、リポジトリへの**すべての**リクエストで Copilot に送信される単一の指示ファイルです。技術スタック、開発中の内容の概要、ベストプラクティス、全般的なガイダンスなど、ほとんどの Copilot Chat または CLI リクエストに関係するプロジェクトレベルの情報を記載します。
- `.github/instructions/*.instructions.md` は、特定のタスクやファイル形式を対象に作成できます。TypeScript や Astro などの言語、UI コンポーネントやユニットテストの作成といったタスク向けのガイドラインを記載できます。

> [!NOTE]
> IDE では、指示ファイルは Copilot Chat のコード生成にのみ使われ、コード補完や次の編集候補には使われません。
>
> Copilot Chat、Copilot CLI、Copilot cloud agent は、コード生成時にリポジトリレベルの指示と `applyTo` フロントマターがある `*.instructions.md` ファイルの両方を使います。
>
> Copilot は AGENTS.md や CLAUDE.md など、ほかの標準形式の指示ファイルにも対応しています。[対応する形式][custom-instructions-support]を参照してください。

### 指示ファイルを管理するためのベストプラクティス

このワークショップでは指示ファイルの作成方法を詳しく扱いませんが、サンプルプロジェクトには代表的な構成例があります。

- `copilot-instructions.md` には、構築中の内容、プロジェクト構成、全体的なコーディング標準など、プロジェクトレベルのガイダンスを記載します。
- `*.instructions.md` には、ユニットテスト、Astro コンポーネント、データ層など、特定のファイル形式やタスクに関する指示を記載します。
- 自然な言葉を使い、明確なガイダンスとコードの例を示します。

指示ファイルの作成方法に唯一の正解はありません。AI の使い方と同じように、試行錯誤を通じてプロジェクトに合う方法を見つけてください。

> [!TIP]
> GitHub Copilot を使うプロジェクトには、充実した指示ファイルを用意することをお勧めします。このプロジェクトには、[UI の更新][ui-instructions]や [Astro][astro-instructions]など、さまざまなタスク向けのファイルがあります。
>
> Copilot に指示ファイルを作成させることもできます。利用する画面ごとに操作は異なります。たとえば VS Code では **Configure Chat → Generate Agent Instructions**、Copilot CLI では `/init` を使います。詳細は各画面に対応するレッスンで説明します。
>
> ひな形や出発点を探している場合は、指示ファイル、カスタムエージェント、その他のリソースを集めた [awesome-copilot][awesome-copilot] を確認してください。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## このプロジェクトのカスタム指示ファイルを確認する

このリポジトリに含まれている指示ファイルを読みます。中心となる `copilot-instructions.md` と、さまざまなタスク向けの `*.instructions.md` ファイルがあります。エディターまたは GitHub の Web UI で開いてください。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの簡単な説明と、**Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** にある **GitHub Actions Workflows** の補足ガイダンスも確認してください。これらは Copilot とのあらゆるやり取りに適用されます。
3. `.github/instructions` フォルダーを開き、ファイルを確認します。Astro ファイル、Drizzle のデータ層、テストなどに関する指示があります。
4. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドは、リポジトリルートからの glob で適用対象を指定します。この例では `**/*.test.ts` に一致する TypeScript テストファイルが対象です。
5. このプロジェクトでユニットテストを作成する際の指示を確認します。
6. 最後に `.github/instructions/drizzle.instructions.md` を開き、末尾までスクロールします。`unit-tests.instructions.md` などの指示ファイルや、プロジェクト内の既存ファイルへのリンクがあります。これにより、大きな指示を再利用できる小さなファイルに分割し、コード生成の例を Copilot に示せます。ここにあるパスはリポジトリルートではなく、指示ファイルからの相対パスです。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションにはコーディング標準が記載されていますが、コード内ドキュメントの要件はまだありません。次の手順で、TSDoc コメントとファイルヘッダーのルールを追加します。

## リポジトリ標準を追加する

この演習で行う編集は、コード内のドキュメントを TSDoc コメントとファイルレベルのコメントヘッダーとして記述する、プロジェクト全体のルールを追加することです。後の演習で cloud agent が Issue に取り組むとき、このルールを参照します。

編集する前に、作業用ブランチを作成します。cloud agent は Issue の対象ブランチから指示を読み取りますが、ここでは変更を整理してコミットします。

1. codespace のターミナルで、次のコマンドを実行して新しいブランチを作成し、切り替えます。

   ```bash
   git checkout -b custom-instructions-cloud
   ```

`.github/copilot-instructions.md` は Copilot にプロジェクトレベルの情報を伝えるファイルです。コーディング標準を記載し、コード提案を改善しましょう。

1. `.github/copilot-instructions.md` を開き直します。
2. 27 行目付近にある **Code formatting requirements** セクションを探します。コーディング標準が記載されていますが、コード内ドキュメントのルールはありません。そのため、生成されたヘルパーにドキュメントコメントがありませんでした。
3. 既存の標準のすぐ下に次の Markdown を追加し、Copilot にファイルコメントヘッダーと TSDoc コメントを追加するよう指示します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 前のレッスンで確認したように、リポジトリ全体の指示は `.github/copilot-instructions.md` に、言語、ファイル形式、タスクに特化した指示は `*.instructions.md` に記載できます。ドキュメントコメントのルールのようなプロジェクト全体の標準には、リポジトリレベルのファイルが適しています。

## 指示の更新をコミット、プッシュ、マージする

cloud agent は、Issue の対象ブランチから指示ファイルを読み取ります。次の演習で Copilot に Issue を割り当てると、`main` からブランチが作成されます。そのため、cloud agent が指示を使えるように、変更を `main` に取り込む必要があります。

1. ステージしてコミットします。

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. ブランチをプッシュします。

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. github.com で `custom-instructions-cloud` から `main` へのプルリクエストを作成してマージします。次の演習で Issue を割り当てると、cloud agent はこの指示を読み取ります。

> [!TIP]
> このワークショップで `main` に直接作業する場合は、ブランチ作成を省略して `main` に直接コミットできます。ブランチを使う手順は、実際のプロジェクトでの作業方法に近づけるためのものです。

## まとめと次のステップ

Copilot がこのプロジェクトの指示ファイルからコンテキストを得る仕組みを確認し、リポジトリ全体の標準を `.github/copilot-instructions.md` に追加しました。このハーネスの後半で cloud agent が作成するプルリクエストをレビューするときに、この標準が適用されていることを確認できます。

次に、[最初の Issue を Copilot cloud agent に割り当てます][next-lesson]。

## リソース

- [GitHub Copilot のカスタマイズに使う指示ファイル][instruction-files]
- [カスタム指示の作成に関するベストプラクティス][instructions-best-practices]
- [Copilot 向けのより良いカスタム指示を作成する 5 つのヒント][copilot-instructions-five-tips]
- [Awesome Copilot — 指示ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
[awesome-copilot]: https://github.com/github/awesome-copilot
