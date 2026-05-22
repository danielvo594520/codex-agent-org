# Code Review Task Example

Reviewer に差分レビューを依頼するためのコピペ用例です。

````md
# Task

## Goal

通知設定追加の差分をレビューする。

## Context

Worker が通知設定 UI とテストを追加しました。
採用前にバグ、リグレッション、テスト不足を確認してください。

## Scope

`git diff` の内容をレビューする。

## Files You Own

- 変更禁止。レビューのみ。

## Files You Must Not Touch

- すべてのファイル

## Constraints

- 称賛ではなく問題発見を優先してください
- 重要度順に指摘してください
- 根拠となるファイルや行を示してください
- 推測の場合は推測と明記してください
- 自動 commit / push / deploy はしないでください

## Expected Output

- 重要度順の指摘
- テスト不足
- 仕様確認が必要な点
- 採用判断の提案

## Verification

```bash
git diff
```

## Final Report Format

```md
指摘:
- [P1] `file:line` ...
- [P2] `file:line` ...

テスト不足:
- ...

仕様確認が必要な点:
- ...

総評:
- 採用可 / 修正後採用 / 採用不可
```
````
