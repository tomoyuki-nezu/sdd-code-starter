# Claude Code Starter Kit

SDD（仕様書ドリブン開発）と Claude Code を使った自律的開発環境のスターターキットです。

## このキットについて

以下のプロジェクトで構築・検証した汎用コンポーネントをまとめたものです：

- Claude Code による自動コード生成
- GitHub Actions による CI/CD（プロジェクト生成後に追加する想定）
- Docker によるローカル開発環境（任意）
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
│   ├── skills/                # Anthropic 正式仕様：フォルダ＋SKILL.md
│   │   ├── git-workflow/
│   │   │   └── SKILL.md
│   │   ├── error-handling/
│   │   │   └── SKILL.md
│   │   ├── test-workflow/
│   │   │   └── SKILL.md
│   │   ├── doc-writer/
│   │   │   └── SKILL.md
│   │   ├── question-doc/
│   │   │   └── SKILL.md
│   │   └── project/           # プロジェクト用テンプレート（任意）
│   │       ├── python-lambda/
│   │       │   └── SKILL.md
│   │       └── sam-architect/
│   │           └── SKILL.md
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
        └── security-settings.md
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
| `<FRAMEWORK>` | フレームワーク | FastAPI / Express |
| `<DATASTORE>` | データストア | PostgreSQL / Firestore など |
| `<IAC_TOOL>` | IaC ツール | Terraform / CloudFormation など |
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

- CLAUDE.md のプロジェクト固有設定は必ず書き換えること
- `env.local.json` / `.env.local` / `.env.production` はプロジェクトごとに新規作成すること（このキットには含めない）
- クラウドの認証情報・シークレット・API キーは絶対にコミットしないこと
- `.claude/settings.json` の sandbox は Claude Code の再起動後に反映される場合がある
