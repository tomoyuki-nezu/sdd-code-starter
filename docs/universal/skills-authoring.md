# Skills の書き方（Anthropic 公式ガイド準拠）

このリポジトリの `.claude/skills/` は、[The Complete Guide to Building Skills for Claude](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf)（Anthropic）の技術要件に沿って整備する。

## フォルダ構成（公式）

各スキルは **kebab-case のフォルダ 1 つ**とする。

```
skill-name/
├── SKILL.md          # 必須。YAML frontmatter + Markdown 本文
├── scripts/          # 任意。実行コード
├── references/       # 任意。必要時だけ読む参照ドキュメント
└── assets/           # 任意。テンプレート等
```

- フォルダ内に **README.md は置かない**（公式禁止）。人間向け説明はリポジトリルートの README に書く。
- `SKILL.md` のファイル名は **SKILL.md のみ**（大文字小文字を含め完全一致）。

## Progressive disclosure（3 層）

1. **frontmatter（name / description 等）** — いつスキルを使うかの判断用。常にコンテキストに載りやすいので短く正確に。
2. **SKILL.md 本文** — スキルが選ばれたときの要約と「詳細は references を読む」へのポインタ。
3. **references/** — 手順全文・表・チェックリスト。トークン節約のため、必要になったら読む。

## frontmatter（必須・推奨）

- **name**（必須）: kebab-case。**フォルダ名と一致**させる。`claude` や `anthropic` を **name に含めない**（予約・セキュリティ制約）。
- **description**（必須）: **何をするか**と**いつ使うか（トリガー表現）**の両方を含める。1024 文字以内。**description 内に山括弧の XML 風タグ（半角の小なり・大なり）は使わない**。
- **license**（任意）: 例 `MIT`。
- **compatibility**（任意）: Claude Code か、必要なツールやネットワーク要件を 500 文字以内で。
- **metadata**（任意）: author, version 等。

Claude Code 向けの `allowed-tools` 等はプロダクト拡張として付けてよいが、**name / description は公式の必須要件を優先**する。

## 本文の書き方

- 具体コマンド・チェックリストは `references/` に寄せ、SKILL.md は短く保つ。
- バンドルしたファイルは **スキルディレクトリからの相対パス**で参照する（例: `references/workflow.md`）。
- 他スキルや `docs/universal/` を参照するときはリポジトリルートからのパスでよい。

## 変更時のテスト（公式の考え方）

スキルを変えたら、次を目安に確認する：

1. **トリガー** — 依頼文で意図したときにこのスキルが選ばれるか。無関係な話題で誤発火しないか。
2. **機能** — 手順どおりに完了するか。エラー時の分岐が動くか。
3. **トークン** — references に逃がしたあと、不要な長文が毎回載っていないか。

## 参考リンク

- [The Complete Guide to Building Skills for Claude（PDF）](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf)
