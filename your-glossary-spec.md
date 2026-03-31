# your-glossary 仕様書

Claude Code 用の単語帳カスタム Slash コマンド

## 概要

プログラミング学習中に出会った知らない単語を、Claude Code 上でサッと調べて保存できるツール。
保存した単語はリスト表示や削除も可能。GitHub で公開し、誰でも利用できるようにする。

## コマンド一覧

### `/word-add <単語>`

- 指定した単語の意味を Claude の知識ベースで生成し、ローカルファイルに保存する
- 既に登録済みの単語の場合は、その旨を通知して上書きするか確認する
- 出力例:
  ```
  ✅ "decorator" を追加しました
  ---
  decorator（デコレータ）: 関数やクラスの振る舞いを変更・拡張するための構文。
  Pythonでは @記号を使って関数の前に記述する。
  ```

### `/word-list`

- 保存済みの全単語とその意味を一覧表示する
- 登録件数が 0 件の場合はその旨を表示する
- 出力例:
  ```
  📖 単語帳（3件）
  ---
  1. decorator — 関数やクラスの振る舞いを変更・拡張するための構文。…
  2. middleware — リクエストとレスポンスの間に処理を挟む仕組み。…
  3. idempotent — 同じ操作を何度実行しても結果が変わらない性質。…
  ```

### `/word-remove <単語>`

- 指定した単語をリストから削除する
- 存在しない単語を指定した場合はエラーメッセージを表示する
- 出力例:
  ```
  🗑️ "decorator" を削除しました
  ```

### `/word-search <キーワード>`

- 保存済みの単語から、キーワードに一致する単語を検索して表示する
- 単語名・意味の両方を検索対象とする
- 該当する単語がない場合はその旨を表示する
- 出力例:
  ```
  🔍 "function" の検索結果（2件）
  ---
  1. decorator — 関数やクラスの振る舞いを変更・拡張するための構文。…
  2. callback — 他の関数に引数として渡される関数。…
  ```

### `/word-export`

- 保存済みの全単語を Markdown 形式に変換し、ファイルとして保存する
- 出力先: カレントディレクトリに `glossary-export.md` を生成する
- 登録件数が 0 件の場合はその旨を表示する
- 出力例:
  ```
  📤 単語帳を glossary-export.md にエクスポートしました（3件）
  ```

## データ仕様

### 保存先

```
~/.your-glossary/glossary.json
```

- `~/.your-glossary/` ディレクトリが存在しない場合は自動作成する
- ホームディレクトリに保存するため、全プロジェクトで共通の単語帳として使える
- Windows の場合は `C:\Users\<ユーザー名>\.your-glossary\` に保存される

### データ構造

```json
{
  "version": "1.0",
  "words": [
    {
      "term": "decorator",
      "meaning": "関数やクラスの振る舞いを変更・拡張するための構文。Pythonでは @記号を使って関数の前に記述する。",
      "addedAt": "2026-03-31T12:00:00Z"
    }
  ]
}
```

| フィールド | 型 | 説明 |
|---|---|---|
| `version` | string | データフォーマットのバージョン |
| `words` | array | 登録済み単語の配列 |
| `words[].term` | string | 単語（原文のまま保持） |
| `words[].meaning` | string | Claude が生成した意味の説明 |
| `words[].addedAt` | string | 追加日時（ISO 8601） |

## 言語の自動判定

- ユーザーの入出力言語（Claude Code のやり取りの言語）に合わせて、意味の説明文の言語を切り替える
- Slash コマンドのプロンプト内で「ユーザーの使用言語に合わせて回答してください」と指示する
- 例: 日本語で作業中 → 日本語で説明 / 英語で作業中 → 英語で説明

## 技術的な実現方法

### Claude Code カスタム Slash コマンド

Markdown ファイルとして配置する。配置先は2箇所から選べる:

- **ユーザー単位**: `~/.claude/commands/` — 全プロジェクトで使える（自分専用）
- **プロジェクト単位**: `<プロジェクト>/.claude/commands/` — そのプロジェクト内でのみ使える（チーム共有可能）

```
# ユーザー単位の場合
~/.claude/
  commands/
    word-add.md      # /word-add コマンド
    word-list.md     # /word-list コマンド
    word-remove.md   # /word-remove コマンド

# プロジェクト単位の場合
<プロジェクト>/.claude/
  commands/
    word-add.md
    word-list.md
    word-remove.md
```

各 `.md` ファイルには、Claude Code に対するプロンプト（指示文）を記述する。
Claude Code はこのプロンプトに従って、ファイルの読み書き・回答生成を行う。

### コマンドプロンプトの方針

- `word-add.md`: 引数の単語を受け取り → 意味を生成 → `glossary.json` を読み込み → 追記して保存
- `word-list.md`: `glossary.json` を読み込み → 整形して一覧表示
- `word-remove.md`: 引数の単語を受け取り → `glossary.json` から該当エントリを削除 → 保存
- `word-search.md`: 引数のキーワードを受け取り → `glossary.json` を検索 → 該当する単語を表示
- `word-export.md`: `glossary.json` を読み込み → Markdown 形式に変換して `glossary.md` として保存

## GitHub 公開に向けて

### リポジトリ構成（案）

```
your-glossary/
├── README.md              # 使い方・インストール手順
├── LICENSE                 # ライセンス（MIT 等）
├── your-glossary-spec.md  # 仕様書
├── .claude/
│   └── commands/
│       ├── word-add.md
│       ├── word-list.md
│       ├── word-remove.md
│       ├── word-search.md
│       └── word-export.md
└── .gitignore
```

### インストール方法（README に記載する内容）

2つのインストール方法を案内する:

#### ユーザー単位（推奨）

- コマンドファイルを `~/.claude/commands/` にコピーする
- 自分のマシン上の **すべてのプロジェクト** で `/word` コマンドが使えるようになる
- 自分だけが使える（他の人には影響しない）
- Git の管理対象にならない

```bash
cp -r your-glossary/.claude/commands/ ~/.claude/commands/
```

#### プロジェクト単位

- コマンドファイルを `<プロジェクト>/.claude/commands/` にコピーする
- **そのプロジェクト内でのみ** 使える
- Git にコミットすれば、プロジェクトをクローンした **全員が使える**
- チームで共有したい場合に向いている

```bash
cp -r your-glossary/.claude/commands/ /path/to/project/.claude/commands/
```

いずれの方法でも、Claude Code を起動し `/word-add <単語>` で利用開始できる

### README に含める情報

- ツールの概要・スクリーンショット
- インストール方法
- 各コマンドの使い方と出力例
- 保存先がホームディレクトリ（`~/.your-glossary/`）である旨の説明
- ライセンス情報

## 将来的な拡張案（スコープ外）

以下は今回のスコープには含めないが、将来的に検討できる機能:

- カテゴリ・タグによる分類
- 登録済み単語のクイズ機能
