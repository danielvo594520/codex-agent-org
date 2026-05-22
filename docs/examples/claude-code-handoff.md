# Claude Code Handoff Example

Claude Code Agent に独立タスクを渡すためのコピペ用例です。

```md
# Claude Code Agent Task

あなたは Codex Leader の外部実行エージェントです。
指定された作業だけを行ってください。

## Goal

既存の設定画面実装を調査し、通知設定を追加する場合の実装方針を提案する。

## Working Directory

`/path/to/worktree`

## Allowed Files

- `src/settings/**`
- `src/shared/form/**`
- `src/api/user/**`

## Forbidden Actions

- git push
- git reset --hard
- deploy
- release
- secret の表示
- 本番環境操作
- 担当外ファイルの編集

## Task

1. 設定画面の既存構成を調査する
2. 保存処理の既存パターンを調査する
3. 通知設定追加に必要な変更候補を整理する
4. 実装する場合の担当ファイル案を出す
5. リスクと確認すべき点を出す

今回はコード変更しないでください。

## Expected Final Report

- 実施内容
- 参照ファイル
- 実装方針案
- リスク
- Codex Leader が確認すべき点
```

実行例:

```bash
cd /path/to/worktree
claude -p \
  --permission-mode default \
  --output-format json \
  "$(cat tasks/notification-settings-research.md)"
```
