# Claude Code セキュリティ設定ガイド

## 概要

Claude Code はターミナルコマンドの実行・ファイルの読み書きが
できるため、セキュリティ設定が重要です。
本ガイドではスターターキットに含まれるセキュリティ設定の
内容と、プロジェクト固有のカスタマイズ方法を説明します。

## 設定ファイルの優先順位

Claude Code の設定は以下の優先順位で適用されます：

1. Managed Settings（組織管理者が設定・上書き不可）
2. User Settings（~/.claude/settings.json）
3. Project Settings（.claude/settings.json）← このキットの設定

## .claude/settings.json の主要設定

### サンドボックス（sandbox）

Claude Code が実行する Bash コマンドを OS レベルで隔離します。

- macOS: Seatbelt による隔離
- Linux: Bubble Wrap による隔離

有効化後は /sandbox コマンドで状態を確認できます。

allowedDomains に含まれていない URL への curl などは失敗します。
プロジェクトで必要なドメインを追加してください：

例：AWS を使うプロジェクトの場合
"allowedDomains" に追加：
  "*.amazonaws.com"
  "*.execute-api.ap-northeast-1.amazonaws.com"

例：GCP / Azure を主に使う場合は、利用する API エンドポイントの
ホスト名（`*.googleapis.com` など）をプロジェクトに合わせて追加する。

### 拒否ルール（permissions.deny）

以下のパターンをデフォルトでブロックしています：

- .env 系ファイルへの Read / Glob / Edit / Write
- Bash 経由の .env 読み取り（`cat` に加え `head` / `tail` / `less` / `more` / `open` / `grep` 等）
- .pem / .key の読み取り（Read および Bash の `cat` / `head` / `tail`）
- `rm -rf` / `chmod 777`

**disableBypassPermissionsMode:** `disable` を設定し、`--dangerously-skip-permissions` による権限バイパスを無効化している（チーム運用時は Managed Settings での固定も推奨）。

ルールの評価順序：deny → ask → allow  
deny ルールは最優先で適用されます。

#### 参考（コミュニティ記事）

`.env` の Glob 禁止や Bash 経路の補強などは、外部解説とも整合させている：

- [Claude Codeのセキュリティ設定を本気で固めた話【2026年版】（Zenn）](https://zenn.dev/momozaki/articles/10cf58b08de335)
- [Claude Codeで行うべきセキュリティ設定 10選（Qiita）](https://qiita.com/miruky/items/51db293a7a7d0d277a5d)

### フック（hooks）

PreToolUse フックで Bash コマンド実行前に
.claude/hooks/validate-command.sh が実行されます。

プロジェクト固有のチェックはこのスクリプトに追加してください。
exit 2 でコマンドをブロック、exit 0 で許可します。

#### PreToolUse フックの前提（`jq`）

`validate-command.sh` は標準入力の JSON からコマンド文字列を取り出すために **`jq` を使用**します。開発マシンに `jq` が入っていないと、フックが失敗し Bash 実行がブロックされることがあります。

- macOS（Homebrew）: `brew install jq`
- 確認: `jq --version`

`jq` を使わないようにフックを書き換えることも可能です（プロジェクト方針に合わせてください）。

### 会話・編集履歴（file-history）

Claude Code は編集バックアップを `~/.claude/file-history/` に保存する場合がある。ディスク使用量が気になる場合は、手動または cron で定期的に削除する（例：`find ~/.claude/file-history -mindepth 1 -delete`）。**リポジトリの設定ではなく利用マシン側の運用**である。

### .env の平文管理を避ける（dotenvx 等）

平文の `.env` をリポジトリに載せない運用として、`dotenvx` 等での暗号化を検討する。導入時は公式手順に従い、**鍵ファイルを `.gitignore` する**こと。

### より強い隔離（devcontainer）

ホストから切り離してエージェントを動かす場合は、Anthropic が示す devcontainer リファレンスの利用を検討する。Claude Code 公式ドキュメントの Development Containers 項を参照すること。

## プロジェクト固有のカスタマイズ

### 許可ドメインの追加

プロジェクトで使う外部サービスのドメインを追加する：

.claude/settings.json の sandbox.network.allowedDomains に追加：
  "*.your-service.com"

### 追加の deny ルール

プロジェクト固有のブロックルールを追加する：

.claude/settings.json の permissions.deny に追加：
  "Bash(your-dangerous-command *)"

### フックの拡張

.claude/hooks/validate-command.sh に追加チェックを記述する：

例：特定の環境変数が設定されている場合のみ許可
if [ -z "$ALLOWED_ENV" ]; then
  echo "Blocked: ALLOWED_ENV is not set" >&2
  exit 2
fi

## チーム・組織での利用

チーム開発では Managed Settings による一括管理が推奨されます。

Managed Settings の配置先：
- macOS: /Library/Application Support/ClaudeCode/managed-settings.json
- Linux: /etc/claude-code/managed-settings.json

Managed Settings は Claude for Teams / Enterprise プランで
リモート配信（Server-managed settings）も可能です。

組織でポリシーを強制する場合の設定キー例（概要。詳細は公式ドキュメントと Qiita 記事を参照）：

| キー（例） | 効果の概要 |
|---|---|
| disableBypassPermissionsMode | 権限バイパスモードの無効化 |
| allowManagedPermissionRulesOnly | Managed 以外の allow/deny を無効化 |
| allowManagedHooksOnly | 管理者フックのみ許可 |
| allowManagedDomainsOnly | 許可ドメインを Managed のみに限定 |

## /permissions コマンドで定期確認

セッション中に許可した「Always allow」ルールが
蓄積していないか定期的に確認してください：

/permissions  # 現在の権限設定を一覧表示
/status       # 読み込まれている設定ファイルを確認
