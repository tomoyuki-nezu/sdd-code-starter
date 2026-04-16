---
name: git-workflow
description: >
  Git の操作を行うとき。「コミットして」「プッシュして」
  「ブランチを作って」などの依頼を受けたとき。
  Conventional Commits 形式でメッセージを生成する。
license: MIT
compatibility: >
  Claude Code。Bash で git を実行する。手順と規約の全文は
  本スキル内 references/conventional-commits.md。
allowed-tools: Bash
metadata:
  author: claude-code-starter
  version: 1.1.0
  category: workflow
---

# Git Workflow

コミット・プッシュ・ブランチ運用を **Conventional Commits（英語メッセージ）** で行う。

## 参照（Progressive disclosure）

手順・type 一覧・禁止事項の**全文**は次を読むこと：

- `references/conventional-commits.md`

本文はトリガー判断用の要約のみとし、実行前に必ず上記を読む。
