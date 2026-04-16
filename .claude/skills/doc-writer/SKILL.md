---
name: doc-writer
description: >
  ドキュメントを作成・更新するとき。「README を更新して」
  「ドキュメントを書いて」「仕様書を整理して」などの
  依頼を受けたとき。日本語で統一されたスタイルで記述する。
license: MIT
compatibility: >
  Claude Code。Read/Write で Markdown を編集。スタイルの全文は
  references/style-guide.md。
allowed-tools: Read Write
metadata:
  author: claude-code-starter
  version: 1.1.0
  category: documentation
---

# Document Writer

日本語ドキュメントの構成・機密の扱い・README セクション・記述スタイルを統一する。

## 参照（Progressive disclosure）

表・README セクション一覧・機密ルールの**全文**は次を読むこと：

- `references/style-guide.md`

執筆前に必ず上記を読む。コミットメッセージは `git-workflow` Skill に従う。
