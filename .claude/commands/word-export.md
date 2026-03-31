# /word-export コマンド

単語帳の内容を Markdown ファイルにエクスポートするコマンドです。

## 手順

1. プロジェクトルートの `.your-glossary/glossary.json` を読み込んでください。
   - ファイルが存在しない場合は「📖 単語帳がまだ作成されていません。エクスポートする単語がありません。」と表示して終了してください。

2. `words` 配列が空（0件）の場合は「📖 単語帳は空です。エクスポートする単語がありません。」と表示して終了してください。

3. 以下の Markdown 形式でファイルを生成してください：

   ```markdown
   # 単語帳

   > エクスポート日時: <現在の日時（YYYY-MM-DD HH:MM 形式）>
   > 登録単語数: <件数>件

   ---

   ## <term>

   <meaning>

   *登録日: <addedAt を YYYY-MM-DD 形式に変換>*

   ---

   ## <term>

   <meaning>

   *登録日: <addedAt を YYYY-MM-DD 形式に変換>*

   ---
   ```

   - `addedAt` の日時順（古い順）で並べてください。

4. 生成した Markdown を `.your-glossary/glossary-export.md` に保存してください。

5. 以下の形式で結果を表示してください：
   ```
   📝 単語帳を Markdown にエクスポートしました（<件数>件）
   保存先: .your-glossary/glossary-export.md
   ```
