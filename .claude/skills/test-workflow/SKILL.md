---
name: test-workflow
description: >
  テストを実行するとき。「テストして」「テストを実行して」
  「pytest を動かして」などの依頼を受けたとき。
  ユニットテストと E2E テストの実行を管理する。
license: MIT
compatibility: >
  Claude Code。Bash でテストコマンドを実行。手順の全文は
  references/runbook.md。プロジェクトのコマンドは CLAUDE.md 等で確認。
allowed-tools: Bash
metadata:
  author: claude-code-starter
  version: 1.1.0
  category: workflow
---

# Test Workflow

テスト実行と結果報告。**コマンドはプロジェクト定義を確認してから**実行する。

## 参照（Progressive disclosure）

前提確認・ユニット/E2E・失敗時の切替・報告形式の**全文**は次を読むこと：

- `references/runbook.md`

実行前に必ず上記を読む。複雑な失敗は `error-handling` Skill に従う。
