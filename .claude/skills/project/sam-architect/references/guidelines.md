# AWS SAM — 詳細ガイドライン

SKILL の本文は `SKILL.md`。このファイルは必要時に読み込む参照層。

## 適用範囲

この Skill は AWS SAM を採用した場合のみ有効とする。
GCP（Cloud Functions / Cloud Run）、Azure Functions など別プロバイダのときはこの Skill を使わず、同様のフォルダ構成でプロジェクト専用 Skill を追加すること。

## 適用前の確認

質問ドキュメントまたは Human の回答で次が明らかであること：

- デプロイ対象が AWS であること
- IaC として SAM（template.yaml 等）を使うこと

## 設計上の原則

- アカウント ID・スタック名の実値をリポジトリに書かない
- 本番スタックへのデプロイは Human の明示許可があるまで自動実行しない
- docs/universal/security-settings.md と .claude/settings.json の制約を順守する

## 参照

- spec/ の API・インフラ要件
- python-lambda Skill（Python 実装と併用する場合）
