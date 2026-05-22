# Claude Code Smoke Test

あなたは Codex Leader の外部実行エージェントです。
このタスクは Claude Code Agent を非対話で呼び出せるか確認するための smoke test です。

## Goal

`codex-agent-org` の基本方針を読み取り、Claude Code Agent として安全側に動作できることを確認する。

## Working Directory

`/Users/miyazawa/workspace/codex-agent-org`

## Allowed Files

- `README.md`
- `AGENTS.md`
- `docs/handoff-contract.md`
- `docs/safety-rules.md`

## Forbidden Actions

- ファイル編集
- git commit
- git push
- git reset --hard
- deploy
- secret の表示
- 本番環境操作

## Task

1. `README.md` と `AGENTS.md` を確認する
2. このリポジトリの目的を 3 行以内で要約する
3. Claude Code Agent が守るべき禁止事項を 5 つ挙げる
4. Codex Leader が成果物を採用する前に確認すべきことを 3 つ挙げる

コードやドキュメントは変更しないでください。

## Expected Final Report

- 要約
- Claude Code Agent の禁止事項
- Codex Leader の確認事項
- 変更有無
