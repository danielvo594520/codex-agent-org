# Feature Development Workflow

機能開発を複数エージェントで進めるための標準ワークフローです。

## When to use

- 追加したい機能の目的が明確
- 変更範囲を複数の独立タスクに分けられる
- テストやレビューまで含めて進めたい

使わないケース:

- 仕様が曖昧
- ユーザー確認が必要な仕様判断が残っている
- 本番環境操作が必要

## Steps

1. Leader が機能の目的、対象ユーザー、完了条件を整理する
2. Explorer が既存実装と影響範囲を調査する
3. Leader が実装範囲と担当ファイルを決める
4. Worker が限定範囲を実装する
5. Worker または Leader がテストを追加する
6. Reviewer が差分を確認する
7. Integrator が変更全体を確認する
8. Leader がテスト、diff、残リスクを確認して採用判断する

## Agents to use

- Explorer: 既存実装、関連ファイル、影響範囲の調査
- Worker: 機能実装、テスト追加
- Reviewer: バグ、リグレッション、テスト不足の確認
- Integrator: 変更全体の確認
- Claude Code Agent: 代替実装案やドキュメント草案が必要な場合

## Handoff examples

```md
# Task

## Goal

ユーザーが通知メールの受信可否を設定できるようにする。

## Context

既存の設定画面は `src/settings` 配下にある。
保存処理は既存の profile settings と同じ API パターンに合わせる。

## Scope

通知メール設定の UI、保存処理、テストを追加する。

## Files You Own

- `src/settings/NotificationSettings.tsx`
- `src/settings/NotificationSettings.test.tsx`

## Files You Must Not Touch

- `src/billing/*`
- `src/auth/*`

## Constraints

- API の破壊的変更をしない
- 既存 UI コンポーネントを使う
- 自動 commit / push をしない

## Expected Output

- 実施内容
- 変更ファイル
- テスト結果
- 残リスク

## Verification

`npm test -- NotificationSettings`
```

## Verification

- 変更対象のテスト
- 関連する既存テスト
- lint または typecheck
- UI 変更がある場合は表示確認

## Done criteria

- 機能要件を満たしている
- 既存パターンに沿っている
- テストが追加または更新されている
- Reviewer の重大指摘が解消されている
- Leader が diff を確認済み
