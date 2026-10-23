---
title: "レッスン 4 - カスタム指示で Copilot を導く"
description: "リポジトリの指示を確認し、ドキュメント標準を追加して、フィルター機能のコードに適用します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

生成 AI を使ううえで、コンテキストは重要です。特定の方法でタスクを実行する必要がある場合は、そのガイダンスを Copilot が利用できるようにします。[指示ファイル][instruction-files]には、希望するコードだけでなく、コードの構成方法も記載します。フィルター機能を作成したので、Copilot が使った指示を確認し、ドキュメント標準を追加してコードに適用します。

このレッスンでは、次の内容を学習します。

- リポジトリの指示とパスに応じた指示ファイルがエージェントに適用される仕組みを確認します。
- コーディング標準に従うよう指示ファイルを更新します。
- 指示ファイルがコードに与える影響を確認します。

## シナリオ

Tailspin Toys には、開発方法に関するガイドラインと要件があります。

- コメントではコードを言い換えるのではなく、意図や自明でない判断を説明します。
- `db/` と `src/lib/` のエクスポートされた関数には、`db` 引数を注入できる場合はその内容も含め、目的、パラメーター、戻り値を TSDoc/JSDoc で記述します。
- 再利用可能な Astro コンポーネントには `Props` の契約を記載し、関連コードの変更に合わせてコメントを最新に保ちます。
- 既存の書式と lint のガイダンスを維持します。

指示ファイルを使って、これらの方針に沿った作業に必要な情報を Copilot に伝えます。

## 指示ファイル

カスタム指示を使うと、コーディングスタイルや要件に関するコンテキストと設定を Copilot に伝え、より適切な提案やコードスニペットを得られます。推奨する規約、ライブラリ、コードコメントの形式などを指定できます。リポジトリ全体、または特定の種類のファイルを対象に設定できます。

指示ファイルには 2 種類あります。

- `.github/copilot-instructions.md` は、リポジトリへの**すべての**リクエストで Copilot に送信されます。ほとんどのリクエストに関係するプロジェクトレベルの情報を記載します。
- `.github/instructions/*.instructions.md` は、特定の言語、ファイル形式、タスクに関するガイドラインを提供します。

> [!NOTE]
> その他の指示形式や対応状況は、環境によって異なります。特定の形式に依存する前に、[カスタム指示の対応状況][custom-instructions-support]を確認してください。

## このプロジェクトのカスタム指示ファイルを確認する

スタータープロジェクトには、すでにいくつかの指示ファイルがあります。変更の効果を確認するため、まず既存のファイルを確認しましょう。

1. Codespace に戻ります。
2. ターミナルではなく Codespaces エディターで `.github/copilot-instructions.md` を開きます。
3. プロジェクトの概要とコーディングガイダンスを確認します。この指示は、このリポジトリでの Copilot とのすべてのやり取りに適用されます。
4. `.github/instructions` フォルダーを開き、ファイルを確認します。Astro ファイル、Drizzle のデータ層、テストなどに関する指示があります。
5. `.github/instructions/unit-tests.instructions.md` を開き、先頭の `applyTo` フィールドを確認します。この glob で適用対象のファイルが決まります。
6. `.github/instructions/drizzle.instructions.md` を開き、ほかの指示ファイルや既存のプロジェクトファイルへのリンクを確認します。大きな指示を再利用できる小さなファイルに分割し、コード生成の例を Copilot に示せます。

## チームのガイダンスに合わせて指示ファイルを更新する

既存のファイルはよい出発点ですが、まだ不足があります。`copilot-instructions.md` にルールを追加し、新しく生成する TypeScript に TSDoc コメントが追加されるようにします。

1. `.github/copilot-instructions.md` で **Code formatting guidance** セクションを探します。35 行目付近にあります。
2. 最後の箇条書きとして次の内容を追加します。

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

ファイルは自動的に保存されます。

## 更新後のガイダンスを使う

Copilot CLI は会話の開始時にリポジトリの指示を読み込みます。更新後の指示を読み込ませるため、フィルター機能のコンテキストを保持したまま会話を終了して再開します。

1. フィルター機能の会話がまだ開いている場合は `/exit` を入力して閉じます。Copilot CLI は会話を自動的に保存します。
2. `game-filters-cli` ブランチのリポジトリルートで、保存済みの会話を再開します。

   ```bash
   copilot --resume
   ```

   プロンプトが表示されたらフィルター機能の会話を選択し、`game-filters-cli` ブランチにいることを確認します。
3. 次のプロンプトで、Copilot に更新後の指示を適用するよう依頼します。

   ```plaintext
   We just updated our instructions and code guidance. Can you please update the code you generated to match that guidance?
   ```

4. `/diff` を入力し、変更された TypeScript ファイルを確認します。追加された TSDoc コメントがコードを正確に説明していることを確認します。

## まとめと次のステップ

Copilot が指示ファイルからコンテキストを得る仕組みを確認し、新しい標準を機能に適用しました。具体的には、次の内容を学習しました。

- リポジトリの指示とパスに応じた指示ファイルがエージェントに適用される仕組み。
- コーディング標準に従うよう指示ファイルを更新する方法。
- 指示ファイルがコードに与える影響。

次に、[再利用可能な quality-checks skill をカスタマイズして実行し][next-lesson]、lint とテストが一貫して実行されるようにします。

## リソース

- [Copilot CLI にカスタム指示を追加する][instruction-files]
- [カスタム指示の対応状況][custom-instructions-support]
- [カスタム指示作成のベストプラクティス][instructions-best-practices]
- [Awesome Copilot — 指示ファイルなどのリソース集][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
