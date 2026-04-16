# 新規プロジェクト初期化ガイド

新しいプロジェクトを開始する際の手順書。スターターキットのコピーから Claude Code による自律実行まで、**質問ドキュメントを先に作る**流れで完了する。

## クイックスタート（質問ファースト）

### Step 1：リポジトリを用意し、スターターをコピー

**推奨:** 先に空のディレクトリで `git init` し、スターターの**中身だけ**をリポジトリルートにコピーする。

```bash
mkdir -p my-project && cd my-project
git init
# 例: スターターを ../claude-code-starter に置いた場合（.git はコピーしない）
rsync -a --exclude='.git' ../claude-code-starter/ ./
```

`cp` を使う場合の例（カレントがリポジトリルートであること）：

```bash
cp -R /path/to/claude-code-starter/. .
```

注意：`cp -r claude-code-starter/ .` のようにディレクトリ名ごとコピーすると、リポジトリ直下に不要なサブフォルダができることがある。**`/.` で中身のみ**をコピーするか、`rsync` のように `.git` を除外すること。

この時点では **`CLAUDE.md` のプレースホルダーを手で埋めなくてよい**（回答確定後に Claude Code が反映する）。

### Step 2：Claude Code を起動し、プロジェクトを開始する

```bash
claude
```

**短いプロンプト（推奨）** — そのまま貼り付け可：

```
プロジェクトを開始します。
```

`CLAUDE.md` の「新規プロジェクト開始時（質問ファースト）」に従い、Claude Code は **先に**次を Human に伝える：

1. スターターに**既に含まれている**ドキュメント（前提・参照先のパス）
2. このプロジェクトで**あらかじめ整理しておくとよい**ドキュメント（`spec/`、`docs/project/`、ルート `README.md`、質問ドキュメントの役割など）
3. **質問ドキュメント（Q1〜Q7）で決める**内容の概要

そのうえで、`.claude/questions/templates/new-project-template.md` を基に質問ドキュメントを `.claude/questions/` に作成する。

**切り分けを明示する場合（任意）**：

```
プロジェクトを開始します。
既にドキュメントで決まっていることと、質問ドキュメントで決めたいことを分けて伝えてから進めてください。
```

**テンプレを明示する場合（任意）**：

```
プロジェクトを開始します。
docs/universal/question-document-spec.md と
.claude/questions/templates/new-project-template.md に従い、
本日の日付の質問ドキュメントを .claude/questions/ に作成してください。
回答後に「回答を読んで実行して」と伝えます。
```

### Step 3：質問ドキュメントに回答（Cursor）

Claude Code が作成した `.claude/questions/YYYYMMDD-new-project.md` を Cursor で開き、「Human の回答欄」に記入する。

質問内容（7 項目）：Q1 基本情報、Q2 技術スタック、Q3 API 設計、Q4 環境構成、Q5 CI/CD、Q6 ローカル開発環境、Q7 ドキュメント要件。

### Step 4：「回答を読んで実行して」

Claude Code に「回答を読んで実行して」と伝える。以降は `question-doc` Skill に従い、承認後に自律的に以下を実行する想定である：

1. 回答を読み取り、実行内容をサマリー表示
2. ユーザーの承認後、自動実行：
   - `CLAUDE.md` のプロジェクト固有セクション（プレースホルダ）の更新
   - 仕様書の作成（例: `spec/api/<resource>.yaml`）
   - ソースコードの生成（`src/`）
   - テストコードの生成（`tests/`）
   - IaC 定義の作成（回答で選んだツールに応じて）
   - CI/CD 定義の作成（回答で選んだホストに応じて。例: `.github/workflows/`）
   - ローカル環境ファイルの作成
   - E2E テストスクリプトの作成（必要な場合のみ。例: `tests/e2e/test_api.sh`）
   - README.md とプロジェクト固有ドキュメントの作成
3. テスト実行・検証
4. コミット

### Step 5：Human が最終確認

- `CLAUDE.md` の内容が質問回答と一致しているか
- 不要なテンプレート Skill（例: AWS SAM を使わないなら `.claude/skills/project/sam-architect/`）を残すか削除するか

## プロジェクト生成後の開発フロー

初期化が完了したら、以降は以下のフレーズで Claude Code に指示できます:

| やりたいこと | Claude Code への指示 |
|---|---|
| 仕様変更の反映 | `spec/api/xxx.yaml の変更を読み込んで、コードとテストを更新して` |
| テスト実行 | `テストして` |
| エラー修正 | `エラーを解析して` |
| コミット | `コミットして` |
| プッシュ | `プッシュして` |
| ドキュメント更新 | `README を更新して` |

複雑な判断が必要な場合は Claude Code が自動的に質問ドキュメントを作成します。Cursor で回答を記入し「回答を読んで実行して」と伝えてください。

## 初期スキャフォルド完了後に人間が行う作業

以下は Claude Code では実施できないため、手動で行う。

### リモートリポジトリの作成と設定

1. Git ホスト（GitHub 等）でリポジトリを作成
2. CI/CD を使う場合、そのサービスの Secrets 等にデプロイ用の認証情報を登録

### クラウド環境の認証設定（使用するプロバイダーに応じて）

CI からクラウドへのデプロイに必要な認証を設定する。

**OIDC（推奨）:** キーレス認証。CI の OIDC トークンでクラウドの一時認証情報を取得する。

**サービスアカウントキー:** CI の Secrets にキーを登録する。

具体的な設定手順はクラウドプロバイダーと CI ホストのドキュメントを参照すること。

### ローカルデータストアのセットアップ

質問回答で Docker Compose 等を選んだ場合の例：

```bash
docker compose up -d
# プロジェクトに応じてテーブル作成やマイグレーションを実行
```

### リモートリポジトリへのプッシュ

```bash
git remote add origin https://github.com/<YOUR_ORG>/<YOUR_REPO>.git
git push -u origin main
```

## 動作確認チェックリスト

すべての設定が完了したら以下を確認する：

- [ ] プロジェクトで定義されたユニットテストコマンド → 全テストパス
- [ ] ローカル依存を使う場合 → `docker compose up -d` 等で起動
- [ ] ビルド成功（使用するビルドツールで確認）
- [ ] ローカルサーバー起動成功（該当する場合）
- [ ] ローカル E2E（定義されている場合）→ 全テストパス
- [ ] `git push` → CI が成功（CI を使う場合）
- [ ] 本番相当環境への E2E は Human 承認と許可設定の上でのみ実施（コマンドはプロジェクトで定義）

## 汎用ファイルと固有ファイルの分類

| ファイル | 種別 | 引き継ぎ方法 |
|---|---|---|
| `CLAUDE.md` | 半汎用 | コピー直後はプレースホルダのまま。質問回答後に Claude Code が反映し、Human が確認 |
| `spec/constitution.md` | 汎用 | そのまま流用 |
| `.claude/skills/<name>/SKILL.md` | 汎用 | そのまま流用（各 Skill はフォルダ単位） |
| `.claude/skills/project/` | 固有 | スタックに合わせて追加・削除（不要なテンプレートは削除可） |
| `docker-compose.yml` | 汎用 | そのまま流用し、必要に応じてサービスを追加 |
| `.gitignore` | 汎用 | そのまま流用 |
| `docs/universal/` | 汎用 | そのまま流用 |
| `.github/workflows/` | 半汎用 | CI に GitHub Actions を選んだ場合などに生成・バージョン調整 |
| `docs/project/` | 固有 | 新規作成 |
| `README.md` | 固有 | 新規作成または初期化で上書き |
| `tests/e2e/test_api.sh` | 固有 | エンドポイントに合わせて修正（E2E を作る場合） |
