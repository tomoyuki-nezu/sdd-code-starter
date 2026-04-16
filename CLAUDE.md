# CLAUDE.md

---

## プロジェクト固有設定

> このセクションのプレースホルダーは、**質問ドキュメントの回答が確定したあと**に反映する（コピー直後に Human が手で埋める必要はない）。
> 反映は「回答を読んで実行して」のフローで Claude Code が行い、Human が内容を確認する。

### プロジェクト概要
<PROJECT_NAME>
<!-- プロジェクトの目的・概要を記載（質問ドキュメントの回答から転記） -->

### 技術スタック
- 言語: <LANGUAGE>
- フレームワーク: <FRAMEWORK>
- IaC: <IAC_TOOL>
- データストア: <DATASTORE>
- テスト: <TEST_FRAMEWORK>
- CI/CD: <CI_CD_TOOL>

### ディレクトリ構成
- spec/                      : 仕様書（Single Source of Truth）
- src/                       : ソースコード
- tests/                     : テストコード（unit/ / e2e/）
- <!-- IaC 定義ファイル（例: template.yaml）を記載 -->
- docs/universal/            : 汎用ドキュメント（プロジェクト横断）
- docs/project/              : プロジェクト固有ドキュメント
- .claude/skills/<name>/     : 各 Skill は kebab-case フォルダ＋SKILL.md（Anthropic 正式仕様）
- .claude/skills/project/    : プロジェクト固有 Skills（同上）
- .github/ 等                : CI/CD 定義（ホストは質問ドキュメントで決定。例: GitHub Actions ならここ）

### 命名規約
<!-- プロジェクトの命名規約を記載 -->
- API エンドポイント: /<resource>, /<resource>/{id}
- 関数/サービス名: <FUNCTION_NAME>

### 開発環境
<!-- 言語・パッケージマネージャに応じて記載 -->
<!-- 例（Python の場合）: -->
<!-- - バージョン管理: pyenv（`pyenv local` でプロジェクト固定） -->
<!-- - 仮想環境: `.venv/`（`python -m venv .venv` で作成） -->
<!-- - パッケージは必ず仮想環境内にインストールすること（グローバル汚染禁止） -->
<!-- - 依存パッケージ: `src/requirements.txt`（本番用） -->

---

## 汎用設定

> 次のプロジェクトでもそのまま使える共通ルール

### 新規プロジェクト開始時（質問ファースト）

Human が「**プロジェクトを開始します**」「新しいプロジェクトを始めます」「まず質問して」など、新規開始の意図を伝えたら、次の順で進めること。

#### 0. プロジェクト開始時に Human へ必ず伝えること（ドキュメントの整理）

Human が **既にすべてのドキュメントを揃えているとは限らない**。そのため、質問ドキュメントを作成する**前に**（または作成と同じ返答の冒頭で）、次の **3 区分** で整理して口頭（チャット）で伝えること。

1. **スターターに既にあり、このリポジトリで前提として読むドキュメント**（現時点では主に参照・遵守。パスを列挙する）
   - `CLAUDE.md`（汎用ルール・このファイルの指示）
   - `spec/constitution.md`（Green/Yellow/Red Zone）
   - `docs/universal/` 配下（例: `sdd-principles.md`, `question-document-spec.md`, `new-project-bootstrap.md`, `security-settings.md`, `skills-authoring.md`, `agent-workflow.md`, `error-workflow.md`）
   - `.claude/skills/` 各スキルの `SKILL.md` と、必要に応じ **`references/`**（運用ルールの詳細。Anthropic の Progressive disclosure）
   - `.claude/settings.json` / `docs/universal/security-settings.md`（セキュリティ前提）

2. **このプロジェクトの進行にあたり、あらかじめ整理・整備しておくとよいドキュメント（中身はまだ空でもよい）**（役割と配置先を説明する）
   - **仕様の Single Source of Truth:** `spec/`（初期化後は API 仕様などをここに置く想定）
   - **プロジェクト固有の説明:** `docs/project/`（アーキテクチャメモ、運用手順、チーム合意事項など）
   - **リポジトリの入口:** ルート `README.md`（セットアップ・開発フロー）
   - **意思決定の記録:** `.claude/questions/*.md`（質問ドキュメント。テンプレは `.claude/questions/templates/`）

3. **質問ドキュメントで項目として取りにいく内容**（テンプレの Q1〜Q7 に相当。ここで決めたことを後から `CLAUDE.md` や `docs/project/` に反映する）

伝えたうえで、**会話だけで足りる確認**（今日のトピック名、すでに存在する社内ドキュメントの URL など）があれば短く聞き、必要なら質問ドキュメントの「追加コメント」に書き写すよう Human に促す。

#### 1. 質問ドキュメントの作成

上記の整理の**後**（同一メッセージ内でよい）、`.claude/questions/templates/new-project-template.md` と `docs/universal/question-document-spec.md` に従い、**質問ドキュメント**を `.claude/questions/YYYYMMDD-<topic>.md` に作成する。

#### 2. プレースホルダと実行タイミング

- 質問ドキュメントの回答が揃うまで、`CLAUDE.md` のプロジェクト固有セクションは **プレースホルダのままでよい**（推測で埋めない）。
- Human が回答を記入し「**回答を読んで実行して**」と伝えたら、`question-doc` Skill の手順に従い解釈・サマリー・承認を経てから実行する。実行内容に **このセクションのプレースホルダ更新** と、必要に応じた **`docs/project/` ・ `spec/` の初版** を含めること。

### コード生成ルール
- すべての関数に型アノテーションを付与すること
- すべての公開関数に docstring を付与すること
- エラーレスポンスは {"error": "メッセージ"} 形式で統一
- ログは print ではなく logger を使用すること

### Git Commit Rules
- コミットメッセージは必ず英語で記述すること
- Conventional Commits 形式に従うこと
- 詳細は `.claude/skills/git-workflow/SKILL.md` を参照

### Git Workflow
- 「コミットして」→ 自律的にコミットを実行（確認不要）
- 「プッシュして」→ 自律的にプッシュを実行
- 詳細は `.claude/skills/git-workflow/SKILL.md` を参照

### エラー修正ルール
- 自動修正の前に必ずユーザーに報告・確認する
- 自動修正は最大 3 回まで
- 詳細は `.claude/skills/error-handling/SKILL.md` を参照

### 質問ドキュメントのルール

複雑な判断が必要な場合は一問一答ではなく質問ドキュメントを作成すること。

**作成のトリガー（以下のいずれかに該当する場合）：**
- 質問数が 3 つ以上になる
- 質問間に依存関係がある
- 決定内容を記録として残す必要がある
- アーキテクチャ・方針に関わる重要な決定

**作成手順：**
1. `.claude/questions/YYYYMMDD-<topic>.md` を作成する
2. `docs/universal/question-document-spec.md` のテンプレートに従って記述する
3. ユーザーに以下の形式で通知する：
   「複数の決定事項があるため質問ドキュメントを作成しました。
    `.claude/questions/YYYYMMDD-topic.md`
    Cursor でファイルを開いて回答を記入してください。
    記入が完了したら『回答を読んで実行して』と伝えてください。」

**読み取りのトリガー（「回答を読んで実行して」と言われた場合）：**
1. 指定された質問ドキュメントを読み込む
2. 回答の解釈を「Claude Code が実行するアクション」欄に記入する
3. 実行内容のサマリーをユーザーに提示して確認を取る
4. 承認後に実行する
5. 完了後にステータスを「実行済み」に更新してコミットする

### 禁止事項
- ハードコードされたクラウドアカウント ID・リソース識別子
- ハードコードされたシークレット・パスワード
- テストコード内の実際のクラウドリソースへのアクセス

### コンテキスト管理ルール

Cursor と Claude Code 間でコンテキストを効率よく渡すためのファイルを使い分ける。

#### ファイル構造

- **`.context/session-log.md`**：直近の作業ログ（ローリング。**個人用**・`.gitignore` 対象）
- **`.context/decisions/`**：重要な設計判断（**チーム共有**・Git コミット対象）
- **`**/.ctx/`**：ディレクトリ単位の作業メモ（**個人用**・原則 `.gitignore`。テンプレとして各ディレクトリに `README.md` のみコミット可）

#### 更新ルール

次のタイミングで **`.ctx/README.md`**（該当ディレクトリの `.ctx/` がある場合）または **`.context/session-log.md`** を更新する：

- **新規ファイル追加時** → 該当ディレクトリの `.ctx/README.md` を更新（なければ必要に応じて `.ctx/` を作成）
- **大きなリファクタリング後** → 該当 `.ctx/` に変更背景を記録、併せて `session-log.md` に一行サマリーでもよい
- **小さなバグ修正** → 更新不要

重要な設計判断は **`.context/decisions/`** にも短く残す（長い議論は質問ドキュメントや `docs/project/` と役割分担）。

#### session-log.md のフォーマット

直近 **3〜5 エントリ**だけ残すローリングとする。古い見出しブロックは削除する。

```
## [最新] YYYY-MM-DD ツール名での作業
- 変更内容を3行以内で記述
- Next: 次のタスク

## [一つ前] YYYY-MM-DD
- ...（古いエントリは随時削除）
```

---

## Skills の読み込み設定

### 汎用 Skills（`.claude/skills/<kebab-case>/SKILL.md`）
どのプロジェクトでもそのまま使える共通ルール。Anthropic 公式の **Progressive disclosure** に従い、手順の全文は各スキル配下の **`references/`** に置き、`SKILL.md` は要約と参照パスのみとする。

スキルの追加・改訂の規約は **`docs/universal/skills-authoring.md`**（公式 PDF へのリンクあり）を参照すること。

| スキル | 用途 |
|---|---|
| `git-workflow` | Git コミット・プッシュの標準ルール |
| `error-handling` | エラー解析・修正ワークフロー |
| `test-workflow` | テスト実行・結果報告 |
| `doc-writer` | ドキュメント生成スタイル |
| `question-doc` | 質問ドキュメントによる意思決定フロー |

### プロジェクト固有 Skills（`.claude/skills/project/`）
プロジェクトに合わせ `kebab-case/SKILL.md` を追加する（README の「カスタマイズ」と `docs/universal/skills-authoring.md` を参照）。

テンプレート例（このリポジトリに同梱）：

| スキル | 用途 |
|---|---|
| `python-lambda` | Python サーバレス実装の規約（プロバイダは質問ドキュメントで確定） |
| `sam-architect` | **AWS SAM を選んだ場合のみ** テンプレート・構成の指針 |

---

## セキュリティ設定

### 設定ファイル
.claude/settings.json にセキュリティのベースライン設定が含まれています。
詳細は docs/universal/security-settings.md を参照してください。

### 重要なルール
- .env ファイル・シークレット・認証情報は絶対に読み取らないこと
- rm -rf コマンドは実行しないこと
- 本番クラウド環境への操作は Human の明示的な許可が必要
- セキュリティ上の問題を発見した場合は即座に Human に報告すること

### /permissions で定期確認
セッション中に権限が蓄積していないか定期的に /permissions で確認すること。
