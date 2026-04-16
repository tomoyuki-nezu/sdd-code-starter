# Claude Code Starter Kit

SDD（仕様書ドリブン開発）と Claude Code を使った自律的開発環境のスターターキットです。

## このキットについて

以下のプロジェクトで構築・検証した汎用コンポーネントをまとめたものです：

- Claude Code による自動コード生成
- CI/CD 定義の追加（質問ドキュメントでホスト・方式を決めた後）
- Docker 等によるローカル開発環境（任意・質問で決定）
- 質問ドキュメントパターンによる意思決定記録

## ディレクトリ構成

```
claude-code-starter/
├── README.md
├── CLAUDE.md
├── .gitignore
├── docker-compose.yml
├── spec/
│   └── constitution.md
├── .claude/
│   ├── settings.json          # セキュリティ設定
│   ├── hooks/
│   │   └── validate-command.sh  # コマンド検証フック
│   ├── skills/                # 各フォルダに SKILL.md（詳細は references/ に分割可）
│   │   ├── git-workflow/
│   │   │   ├── SKILL.md
│   │   │   └── references/
│   │   ├── error-handling/
│   │   │   ├── SKILL.md
│   │   │   └── references/
│   │   ├── test-workflow/
│   │   │   ├── SKILL.md
│   │   │   └── references/
│   │   ├── doc-writer/
│   │   │   ├── SKILL.md
│   │   │   └── references/
│   │   ├── question-doc/
│   │   │   ├── SKILL.md
│   │   │   └── references/
│   │   └── project/           # プロジェクト用テンプレート（任意）
│   │       ├── python-lambda/
│   │       │   ├── SKILL.md
│   │       │   └── references/
│   │       └── sam-architect/
│   │           ├── SKILL.md
│   │           └── references/
│   └── questions/
│       └── templates/
│           └── new-project-template.md
└── docs/
    └── universal/
        ├── new-project-bootstrap.md
        ├── question-document-spec.md
        ├── agent-workflow.md
        ├── error-workflow.md
        ├── sdd-principles.md
        ├── security-settings.md
        └── skills-authoring.md   # Anthropic Skills 公式準拠の書き方
```

## 使い方

### 新規プロジェクトへの適用（質問ファースト）

**推奨フロー:** 空ディレクトリで `git init` → スターターの**中身**をコピー → Claude Code に「**プロジェクトを開始します**」と伝える →（システムが既存ドキュメントと、整備しておくとよいドキュメントを整理して伝える）→ 質問ドキュメントに回答 →「回答を読んで実行して」。

詳細は **`docs/universal/new-project-bootstrap.md`** を参照。

#### 1. リポジトリを用意し、ファイルをコピー

```bash
mkdir -p my-project && cd my-project
git init
# 例: スターターを ../claude-code-starter に置いた場合（.git はコピーしない）
rsync -a --exclude='.git' ../claude-code-starter/ ./
```

`cp` で中身だけをコピーする例：`cp -R /path/to/claude-code-starter/. .`

**この時点では `CLAUDE.md` を手で埋めなくてよい。** プレースホルダは質問ドキュメントの回答が確定したあと、Claude Code が反映する。

#### 2. Claude Code を起動する

```bash
claude
```

#### 3. 最初のプロンプト（短くてよい）

推奨（業務の入口として自然な一文）：

```
プロジェクトを開始します。
```

`CLAUDE.md` の「新規プロジェクト開始時（質問ファースト）」により、**まず**スターターに既にあるドキュメントと、あらかじめ整理しておくとよいドキュメントの役割を Human に伝えたうえで、テンプレートに基づく**質問ドキュメント**を `.claude/questions/` に作成する。

切り分けを明示したい場合の例（任意）：

```
プロジェクトを開始します。
スターターで既に定まっていることと、質問ドキュメントで決めたいことを分けて伝えてから、質問ドキュメントを作成してください。
```

テンプレを明示する例（任意）：

```
プロジェクトを開始します。
docs/universal/question-document-spec.md と
.claude/questions/templates/new-project-template.md に従い、
質問ドキュメントを .claude/questions/ に作成してください。
回答後に「回答を読んで実行して」と伝えます。
```

#### 4. 質問ドキュメントに回答する（Cursor）

#### 5. 「回答を読んで実行して」と Claude Code に伝える

承認フロー後、`CLAUDE.md` の更新やコード生成が進む。

### 開発中によく使うフレーズ

プロジェクト生成後の日常開発で、Claude Code に以下のフレーズで指示できます:

| やりたいこと | Claude Code への指示 |
|---|---|
| 仕様変更の反映 | `spec/api/xxx.yaml の変更を読み込んで、コードとテストを更新して` |
| テスト実行 | `テストして` |
| エラー修正 | `エラーを解析して` |
| コミット | `コミットして` |
| プッシュ | `プッシュして` |
| ドキュメント更新 | `README を更新して` |

複雑な判断が必要な場合は Claude Code が自動的に質問ドキュメントを作成します。Cursor で回答を記入し「回答を読んで実行して」と伝えてください。

### 参照ドキュメント

| ドキュメント | 内容 |
|---|---|
| `docs/universal/sdd-principles.md` | SDD の設計思想・原則 |
| `docs/universal/agent-workflow.md` | エージェントワークフロー設計 |
| `docs/universal/error-workflow.md` | エラー解析・修正ワークフロー |
| `docs/universal/question-document-spec.md` | 質問ドキュメント仕様 |
| `docs/universal/new-project-bootstrap.md` | 新規プロジェクト初期化手順 |
| `docs/universal/security-settings.md` | Claude Code セキュリティ設定ガイド |
| `docs/universal/skills-authoring.md` | Skills 構成（Anthropic 公式 PDF 準拠） |

### プレースホルダ一覧（`CLAUDE.md` ・回答後に反映）

質問ドキュメントの回答を元に Claude Code が埋める想定の項目：

| プレースホルダー | 説明 | 例 |
|---|---|---|
| `<PROJECT_NAME>` | プロジェクト名 | タスク管理 API |
| `<LANGUAGE>` | 使用言語 | Python 3.13 |
| `<FRAMEWORK>` | フレームワーク | FastAPI / Express |
| `<DATASTORE>` | データストア | PostgreSQL など |
| `<IAC_TOOL>` | IaC ツール | Terraform など |
| `<FUNCTION_NAME>` | 関数/サービス名 | TasksService |
| `<TEST_FRAMEWORK>` | テストフレームワーク | pytest / jest |
| `<CI_CD_TOOL>` | CI/CD ホスト | GitHub Actions など |

## カスタマイズ

### セキュリティ設定のカスタマイズ
.claude/settings.json の以下を調整してください：
- sandbox.network.allowedDomains: プロジェクトで使うドメインを追加
- permissions.deny: プロジェクト固有のブロックルールを追加
- .claude/hooks/validate-command.sh: カスタムチェックを追加

詳細は docs/universal/security-settings.md を参照してください。

### プロジェクト固有 Skills の追加
.claude/skills/ に新しいフォルダを作成し、
その中に SKILL.md を配置してください（Anthropic 正式仕様）。

詳細は **`docs/universal/skills-authoring.md`** と [Anthropic の Skills 構築ガイド（PDF）](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf) を参照。

フォルダ名のルール：
- kebab-case を使うこと（例: python-lambda）
- スペース・アンダースコア・大文字は使わないこと
- フォルダ内に README.md は置かないこと

SKILL.md の frontmatter 必須項目：
```
---
name: your-skill-name      # kebab-case・スペース禁止
description: >             # what + when を含める・1024文字以内
  何をするスキルか。
  いつ使うか（トリガーフレーズを含める）。
---
```

プロジェクト専用は `.claude/skills/project/<name>/SKILL.md` に置くと整理しやすいです。

### 質問ドキュメントテンプレートの追加

`.claude/questions/templates/` に用途別のテンプレートを追加できます。

例：

```
error-fix-template.md
architecture-change-template.md
deployment-template.md
```

## 注意事項

- **コピー直後に `CLAUDE.md` を手で全部埋める必要はない**（質問ドキュメントの回答確定後に反映する）
- PreToolUse フック（`validate-command.sh`）は **`jq` が PATH に必要**（未インストールだとフックが失敗する場合がある）。詳細は `docs/universal/security-settings.md`
- `env.local.json` / `.env.local` / `.env.production` はプロジェクトごとに新規作成すること（このキットには含めない）
- クラウドの認証情報・シークレット・API キーは絶対にコミットしないこと
- `.claude/settings.json` の sandbox は Claude Code の再起動後に反映される場合がある
