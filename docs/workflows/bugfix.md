# Bugfix Workflow

バグ修正を安全に進めるための標準ワークフローです。

## When to use

- 再現条件がある、または調査で再現条件を特定できる
- 修正範囲を限定できる
- 回帰テストを追加できる可能性がある

使わないケース:

- 原因が不明なまま大きく直す必要がある
- 本番データの直接操作が必要
- 緊急 release 判断が必要

## Steps

1. Leader が不具合内容、期待動作、再現条件を整理する
2. Explorer が関連ファイルと原因候補を調査する
3. Leader が最小修正方針を決める
4. Worker が修正と回帰テストを追加する
5. Reviewer が修正範囲と副作用を確認する
6. Leader が再現確認、テスト、diff を確認する
7. 残リスクを明記して報告する

## Agents to use

- Explorer: 原因調査、再現条件整理
- Worker: 最小修正、回帰テスト追加
- Reviewer: 副作用、テスト不足、仕様不一致の確認
- Integrator: 複数修正がある場合の統合確認

## Handoff examples

```md
# Task

## Goal

CSV インポート時に空行で処理が失敗する問題を修正する。

## Context

再現条件:
- CSV の末尾に空行がある
- `importUsers` 実行時に validation error になる

## Scope

空行をスキップし、既存の validation は維持する。

## Files You Own

- `src/import/importUsers.ts`
- `src/import/importUsers.test.ts`

## Files You Must Not Touch

- `src/db/*`
- `src/auth/*`

## Constraints

- CSV パーサーのライブラリは変更しない
- 既存の validation 仕様は変えない
- 回帰テストを追加する

## Expected Output

- 原因
- 修正内容
- 変更ファイル
- テスト結果

## Verification

`npm test -- importUsers`
```

## Verification

- バグの再現確認
- 修正後の再実行
- 回帰テスト
- 影響範囲の既存テスト

## Done criteria

- 原因が説明されている
- 最小修正になっている
- 回帰テストがある
- 副作用が確認されている
- Leader が採用判断した
