# Research Workflow

調査タスクを構造化して進めるための標準ワークフローです。

## When to use

- 実装前に現状把握が必要
- 影響範囲や選択肢を整理したい
- 複数観点を並列で調査したい

使わないケース:

- すぐに小さな修正ができる
- 調査対象に secret や本番データが含まれる
- 仕様決定のためにユーザー確認が必要

## Steps

1. Leader が調査目的と質問を定義する
2. Explorer に対象範囲を渡す
3. 必要なら Claude Code Agent に独立調査を渡す
4. Explorer が関連ファイル、既存パターン、制約を整理する
5. Leader が結果を統合する
6. 必要なら Reviewer が調査結果の抜けを確認する
7. Leader が次の実装方針または保留理由を報告する

## Agents to use

- Explorer: 主調査
- Claude Code Agent: 独立した別観点の調査
- Reviewer: 調査結果の抜け確認
- Leader: 統合と判断

## Handoff examples

```md
# Task

## Goal

通知設定を追加する場合の影響範囲を調査する。

## Context

設定画面とユーザー設定保存 API が関係しそう。

## Scope

`src/settings` と `src/api/user` を調査する。

## Files You Own

- `src/settings`
- `src/api/user`

## Files You Must Not Touch

- すべてのファイルは変更禁止

## Constraints

- コード変更しない
- 推測と確認済み事実を分ける

## Expected Output

- 関連ファイル
- 既存パターン
- 影響範囲
- 実装候補
- 不明点

## Verification

該当なし。調査結果の根拠ファイルを明記する。
```

## Verification

- 根拠ファイルが示されている
- 推測と事実が分かれている
- 影響範囲が過不足なく整理されている
- 次アクションが明確

## Done criteria

- Leader が実装方針を決められる
- 未確認事項が明記されている
- 不要なコード変更がない
