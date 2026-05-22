# Parallel Execution Workflow

複数エージェントを一気に動かすための標準ワークフローです。

## When to use

- 調査、実装、レビュー、ドキュメント作成を同時に進めたい
- 独立したファイルやモジュールに分けられる
- Leader が統合と検証を担当できる

使わないケース:

- 仕様が未確定
- 同じファイルを複数人が触る
- 本番環境操作や release が含まれる
- ユーザー確認が必要な意思決定が残っている

## Steps

1. Leader が目的、制約、完了条件を整理する
2. 作業を調査、実装、テスト、レビュー、ドキュメントに分類する
3. 同時実行できる単位に分割する
4. 各 agent に Handoff Contract を渡す
5. Leader はブロッキング作業を直接進める
6. agent の結果を回収する
7. Integrator が diff と想定外変更を確認する
8. Reviewer がリスクを確認する
9. Leader がテストと内容を確認する
10. Leader が採用、修正、破棄を判断する

## Agents to use

- Explorer: 調査、影響範囲確認
- Worker: 独立した実装やドキュメント作成
- Reviewer: 差分レビュー
- Integrator: 複数成果物の統合確認
- Claude Code Agent: 独立調査、草案、代替案作成

## Handoff examples

```md
# Parallel Work Plan

## Goal

ユーザー設定画面に通知設定を追加する。

## Critical Path

Leader が仕様と完了条件を確定する。

## Parallel Tasks

### Agent 1: Explorer

- 目的: 既存の設定画面と保存処理を調査する
- 対象: `src/settings`
- 変更可否: 変更禁止
- 期待出力: 関連ファイル、既存パターン、影響範囲

### Agent 2: Worker

- 目的: 通知設定 UI を追加する
- 担当ファイル: `src/settings/NotificationSettings.tsx`
- 変更可否: 変更可
- 期待出力: 変更内容、変更ファイル、確認結果

### Agent 3: Reviewer

- 目的: 完成後の差分をレビューする
- 対象: `git diff`
- 変更可否: 変更禁止
- 期待出力: 重要度順の指摘

## Integration Plan

- Explorer 結果で実装範囲を確認する
- Worker 差分を読む
- Reviewer 指摘を反映する
- テストを実行する

## Done Criteria

- UI が表示される
- 保存処理が既存パターンに合っている
- テストが通る
```

## Verification

Leader は最後に以下を確認します。

```bash
git status
git diff --stat
git diff
```

必要に応じて、対象のテスト、lint、build、ドキュメント確認を実行します。

## Done criteria

- 各 agent の担当範囲が明確だった
- 想定外ファイルの変更がない
- diff を Leader が読んだ
- 必要な確認コマンドを実行した
- 残リスクが説明されている
- 自動 commit / push / deploy をしていない
