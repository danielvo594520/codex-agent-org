# Bugfix Task Example

バグ修正を Worker に渡すためのコピペ用例です。

````md
# Task

## Goal

CSV インポート時に末尾の空行で処理が失敗する問題を修正する。

## Context

再現条件:
- CSV の末尾に空行がある
- `importUsers` 実行時に validation error になる

期待動作:
- 空行はスキップする
- 既存の validation は維持する

## Scope

空行スキップ処理と回帰テストを追加する。

## Files You Own

- `src/import/importUsers.ts`
- `src/import/importUsers.test.ts`

## Files You Must Not Touch

- `src/db/*`
- `src/auth/*`
- `src/billing/*`

## Constraints

- 他のエージェントの変更を revert しないでください
- CSV パーサーのライブラリは変更しないでください
- 既存の validation 仕様は変えないでください
- 自動 commit / push / deploy はしないでください

## Expected Output

- 原因
- 修正内容
- 変更ファイル
- テスト結果
- 残リスク

## Verification

```bash
npm test -- importUsers
```

## Final Report Format

```md
原因:
- ...

修正:
- ...

変更ファイル:
- ...

確認:
- ...

残リスク:
- ...
```
````
