---
name: sam-architect
description: >
  AWS SAM によるサーバレス構成を設計・編集するとき。
  質問ドキュメントで AWS SAM が IaC として選ばれたプロジェクトで
  template やデプロイ手順の依頼を受けたとき。
allowed-tools: Read Write Bash
metadata:
  author: claude-code-starter
  version: 1.0.0
  category: project
---

# AWS SAM（プロジェクト用テンプレート）

この Skill は **AWS SAM を採用した場合のみ** 有効とする。
GCP（Cloud Functions / Cloud Run）、Azure Functions など別プロバイダのときは
この Skill を使わず、同様のフォルダ構成でプロジェクト専用 Skill を追加すること。

## 適用前の確認

質問ドキュメントまたは Human の回答で次が明らかであること：

- デプロイ対象が AWS であること
- IaC として SAM（`template.yaml` 等）を使うこと

## 設計上の原則

- アカウント ID・スタック名の実値をリポジトリに書かない
- 本番スタックへのデプロイは Human の明示許可があるまで自動実行しない
- `docs/universal/security-settings.md` と `.claude/settings.json` の制約を順守する

## 参照

- `spec/` の API・インフラ要件
- `python-lambda` Skill（Python 実装と併用する場合）
