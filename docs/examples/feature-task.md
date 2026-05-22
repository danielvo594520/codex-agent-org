# Feature Task Example

機能開発を Worker に渡すためのコピペ用例です。

````md
# Task

## Goal

ユーザーが通知メールの受信可否を設定できるようにする。

## Context

既存の設定画面は `src/settings` 配下にあります。
保存処理は既存の profile settings と同じ API パターンに合わせます。

## Scope

通知メール設定の UI、保存処理、テストを追加します。

## Files You Own

- `src/settings/NotificationSettings.tsx`
- `src/settings/NotificationSettings.test.tsx`

## Files You Must Not Touch

- `src/billing/*`
- `src/auth/*`
- `src/shared/api/*`

## Constraints

- あなたは単独ではありません
- 他のエージェントの変更を revert しないでください
- API の破壊的変更をしないでください
- 既存 UI コンポーネントを使ってください
- 自動 commit / push / deploy はしないでください

## Expected Output

- 実施内容
- 変更ファイル
- テスト結果
- 残リスク

## Verification

```bash
npm test -- NotificationSettings
```

## Final Report Format

```md
実施内容:
- ...

変更ファイル:
- ...

確認:
- ...

残リスク:
- ...
```
````
