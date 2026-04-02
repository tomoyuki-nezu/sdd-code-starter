# Claude Code Starter Kit

SDD（仕様書ドリブン開発）と Claude Code を使った自律的開発環境のスターターキットです。

## このキットについて

以下のプロジェクトで構築・検証した汎用コンポーネントをまとめたものです：

- Claude Code による自動コード生成
- GitHub Actions による CI/CD
- Docker によるローカル開発環境
- 質問ドキュメントパターンによる意思決定記録

## ディレクトリ構成

```
claude-code-starter/
├── CLAUDE.md                  # Claude Code への共通指示書
├── .gitignore                 # 除外ファイル設定
├── docker-compose.yml         # ローカル開発サービス設定
├── spec/
│   └── constitution.md        # AI の行動範囲定義（憲法）
├── .claude/
│   ├── skills/common/         # 汎用 Skills
│   │   ├── git-workflow.md    # Git 操作ルール
│   │   ├── error-handling.md  # エラー解析・修正ワークフロー
│   │   ├── test-workflow.md   # テスト実行ワークフロー
│   │   ├── doc-writer.md      # ドキュメント生成スタイル
│   │   └── question-doc.md    # 質問ドキュメントワークフロー
│   └── questions/templates/   # 質問ドキュメントテンプレート
│       └── new-project-template.md
└── docs/universal/            # 汎用ドキュメント
    ├── new-project-bootstrap.md  # 新規プロジェクト初期化手順
    ├── question-document-spec.md # 質問ドキュメント仕様
    ├── agent-workflow.md         # エージェントワークフロー設計
    ├── error-workflow.md         # エラー解析ワークフロー
    └── sdd-principles.md        # SDD の設計思想
```

## 使い方

### 新規プロジェクトへの適用

1. このキットをコピーする

```bash
cp -r claude-code-starter/ <new-project>/
cd <new-project>/
git init
```

2. CLAUDE.md のプレースホルダーを書き換える

| プレースホルダー | 説明 | 例 |
|---|---|---|
| `<PROJECT_NAME>` | プロジェクト名 | タスク管理 API |
| `<LANGUAGE>` | 使用言語 | Python 3.13 |
| `<FRAMEWORK>` | フレームワーク | FastAPI / Express / Lambda |
| `<DATASTORE>` | データストア | PostgreSQL / DynamoDB / MongoDB |
| `<IAC_TOOL>` | IaC ツール | Terraform / SAM / CDK |
| `<FUNCTION_NAME>` | 関数/サービス名 | TasksService |
| `<TEST_FRAMEWORK>` | テストフレームワーク | pytest / jest / vitest |

3. Claude Code を起動して初期化プロンプトを実行する

```
新しいプロジェクトを始めます。
以下のドキュメントを読み込んでください：
- docs/universal/new-project-bootstrap.md
- docs/universal/question-document-spec.md
- .claude/questions/templates/new-project-template.md

読み込んだ後、new-project-template.md を元に
本日の日付で質問ドキュメントを
.claude/questions/ に作成してください。
私が回答を記入したら
『回答を読んで実行して』と伝えます。
```

4. 質問ドキュメントに回答する（Cursor で編集）

5. 「回答を読んで実行して」と Claude Code に伝える

### 参照ドキュメント

| ドキュメント | 内容 |
|---|---|
| `docs/universal/sdd-principles.md` | SDD の設計思想・原則 |
| `docs/universal/agent-workflow.md` | エージェントワークフロー設計 |
| `docs/universal/error-workflow.md` | エラー解析・修正ワークフロー |
| `docs/universal/question-document-spec.md` | 質問ドキュメント仕様 |
| `docs/universal/new-project-bootstrap.md` | 新規プロジェクト初期化手順 |

## カスタマイズ

### プロジェクト固有 Skills の追加

`.claude/skills/project/` ディレクトリを作成し、プロジェクト固有の Skills を追加してください。

```bash
mkdir -p .claude/skills/project/
```

例：

```
.claude/skills/project/python-lambda.md
.claude/skills/project/sam-architect.md
```

### 質問ドキュメントテンプレートの追加

`.claude/questions/templates/` に用途別のテンプレートを追加できます。

例：

```
error-fix-template.md
architecture-change-template.md
deployment-template.md
```

## 注意事項

- CLAUDE.md のプロジェクト固有設定は必ず書き換えること
- `env.local.json` / `.env.local` / `.env.production` はプロジェクトごとに新規作成すること（このキットには含めない）
- クラウドの認証情報・シークレット・API キーは絶対にコミットしないこと
