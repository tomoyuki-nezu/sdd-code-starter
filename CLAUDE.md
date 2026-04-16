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

- Human が「まず質問して」「新しいプロジェクトを始めます」などと伝えたら、**仕様書・ソースコード・CI 定義の生成に着手する前に**、`.claude/questions/templates/new-project-template.md` と `docs/universal/question-document-spec.md` に従い、**質問ドキュメント**を `.claude/questions/YYYYMMDD-<topic>.md` に作成する。
- 質問ドキュメントの回答が揃うまで、`CLAUDE.md` のプロジェクト固有セクションは **プレースホルダのままでよい**（推測で埋めない）。
- Human が回答を記入し「**回答を読んで実行して**」と伝えたら、`question-doc` Skill の手順に従い解釈・サマリー・承認を経てから実行する。実行内容に **このセクションのプレースホルダ更新** を含めること。

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

---

## Skills の読み込み設定

### 汎用 Skills（`.claude/skills/<kebab-case>/SKILL.md`）
どのプロジェクトでもそのまま使える共通ルール：

| スキル | 用途 |
|---|---|
| `git-workflow` | Git コミット・プッシュの標準ルール |
| `error-handling` | エラー解析・修正ワークフロー |
| `test-workflow` | テスト実行・結果報告 |
| `doc-writer` | ドキュメント生成スタイル |
| `question-doc` | 質問ドキュメントによる意思決定フロー |

### プロジェクト固有 Skills（`.claude/skills/project/`）
プロジェクトに合わせ `kebab-case/SKILL.md` を追加する（README の「カスタマイズ」を参照）。

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
