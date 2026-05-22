# Review Workflow

変更を採用する前に、品質とリスクを確認するための標準ワークフローです。

## When to use

- Worker や Claude Code Agent の成果物を採用する前
- 大きめの diff がある
- 仕様との一致やテスト不足を確認したい

使わないケース:

- まだ成果物がない
- レビュー対象が明確でない
- secret を含む差分を外部 agent に見せる必要がある

## Steps

1. Leader がレビュー対象と観点を整理する
2. Reviewer に diff、関連ファイル、仕様を渡す
3. Reviewer が重要度順に指摘する
4. Leader が指摘を採用、保留、却下に分類する
5. 必要なら Worker が修正する
6. Leader が再度 diff とテストを確認する
7. 最終判断を報告する

## Agents to use

- Reviewer: 問題発見
- Worker: 指摘対応
- Integrator: 修正後の統合確認
- Leader: 判断と報告

## Handoff examples

```md
# Task

## Goal

通知設定追加の差分をレビューする。

## Context

Worker が `src/settings/NotificationSettings.tsx` とテストを変更した。

## Scope

機能要件、既存パターン、テスト不足を確認する。

## Files You Own

- 変更禁止。レビューのみ。

## Files You Must Not Touch

- すべてのファイル

## Constraints

- 問題発見を優先する
- 重要度順に出す
- 根拠ファイルを示す

## Expected Output

- 重要度順の指摘
- テスト不足
- 仕様確認が必要な点
- 採用判断の提案

## Verification

`git diff` を読む。
```

## Verification

- 指摘に根拠がある
- 重要度が妥当
- テスト不足が確認されている
- Leader が最終判断した

## Done criteria

- 重大な指摘が解消または明確に保留されている
- 採用判断の根拠がある
- 残リスクが報告されている
