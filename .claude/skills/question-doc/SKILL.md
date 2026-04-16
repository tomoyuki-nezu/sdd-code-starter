---
name: question-doc
description: >
  複数の質問や方向性の確認が必要なとき。
  「回答を読んで実行して」と言われたときに質問ドキュメントを読み取り実行する。
  質問ドキュメントの作成・更新を依頼されたとき。
license: MIT
compatibility: >
  Claude Code。新規開始時は CLAUDE.md の質問ファースト節に従う。
  詳細フローは references/workflow.md。
allowed-tools: Read Write
metadata:
  author: claude-code-starter
  version: 1.1.0
  category: workflow
---

# Question Document Workflow

意思決定を **質問ドキュメント** に記録し、回答確定後に実行する。

## 参照（Progressive disclosure）

いつ使うか／使わないか、作成・読み取り・注意事項の**全文**は次を読むこと：

- `references/workflow.md`

仕様の正本は常に `docs/universal/question-document-spec.md`。
