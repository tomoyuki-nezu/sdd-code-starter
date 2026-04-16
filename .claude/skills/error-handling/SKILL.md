---
name: error-handling
description: >
  テストが失敗したとき、実行エラーが発生したとき。
  「エラーを解析して」「原因を調べて」「テストが失敗している」
  などの依頼を受けたとき。エラーの分析と修正提案を行う。
license: MIT
compatibility: >
  Claude Code。自動修正前に Human へ報告する。詳細ステップは
  references/workflow.md。
allowed-tools: Bash Read
metadata:
  author: claude-code-starter
  version: 1.1.0
  category: workflow
---

# Error Handling Workflow

エラー解析と修正提案。**自動修正は最初に必ず Human の選択を得る。**

## 参照（Progressive disclosure）

分類・Step 1〜4・エスカレーション・質問ドキュメントへの切替の**全文**は次を読むこと：

- `references/workflow.md`

実行前に必ず上記を読む。質問ドキュメントが必要な場合は `question-doc` Skill を併用する。
