# Parallel Feature Smoke Test

あなたは Codex Leader です。
このタスクは、Explorer / Worker / Reviewer / Claude Code Agent を組織的に使う運用リハーサルです。

## Goal

実リポジトリを壊さずに、複数エージェント運用の流れを確認する。

## Scope

- 読み取り中心で進める
- Worker の変更は小さな docs 追加または typo 修正に限定する
- Claude Code Agent は読み取り専用調査だけを行う
- 最後に Leader がログ、diff、変更ファイル、残リスクを確認する

## Out of Scope

- 自動 commit
- 自動 push
- 自動 deploy
- Claude Code Agent の成果物自動採用
- secret を含む prompt やログの保存

## Parallel Work Plan

### Critical Path: Codex Leader

Leader は以下を行う。

1. `AGENTS.md` と `docs/workflows/parallel-execution.md` を確認する
2. 各 agent の担当範囲を明確にする
3. Claude Code Agent 実行時は `scripts/run-claude-agent` を使う
4. 最後に `git status`、`git diff --stat`、run log を確認する
5. 採用、修正、破棄を判断する

### Agent 1: Explorer

目的:

- runner と parallel smoke test に関係する既存ドキュメントを調査する

対象:

- `README.md`
- `AGENTS.md`
- `docs/workflows/claude-code-run.md`
- `docs/workflows/parallel-execution.md`

変更可否:

- 変更禁止

期待出力:

- 関連ファイル
- 既存ルール
- 追記すべき箇所
- 矛盾しそうな点

### Agent 2: Worker

目的:

- 小さな docs 追加または typo 修正だけを行う

担当ファイル:

- Leader が明示した 1 ファイルのみ

変更可否:

- 変更可。ただし 10 行程度まで

禁止:

- 担当外ファイルの編集
- 自動 commit / push
- 他 agent の変更 revert

期待出力:

- 実施内容
- 変更ファイル
- 確認結果
- 残リスク

### Agent 3: Reviewer

目的:

- Worker の小さな diff と Claude Code Agent の run log を確認する

対象:

- `git diff`
- `runs/<timestamp>-smoke-test/`

変更可否:

- 変更禁止

期待出力:

- 重要度順の指摘
- 想定外ファイルの有無
- secret がログに含まれていないか
- 採用可否

### Agent 4: Claude Code Agent

目的:

- 読み取り専用で、既存ドキュメントの整合性を調査する

実行方法:

```bash
scripts/run-claude-agent \
  --prompt tasks/claude-code-smoke-test.md \
  --cwd . \
  --name smoke-test \
  --timeout-sec 120
```

制約:

- ファイル編集禁止
- git commit / push 禁止
- secret の表示禁止
- `--cwd` は Claude Code 側には渡さない

期待出力:

- `runs/<timestamp>-smoke-test/` 配下の run log
- Claude Code Agent の調査結果
- exit code
- diff

## Leader Integration Checklist

```bash
git status
git diff --stat
find runs -maxdepth 2 -type f | sort
cat runs/<timestamp>-smoke-test/exit.json
```

Leader は以下を確認する。

- Worker の変更が担当範囲内か
- Claude Code Agent のログが保存されているか
- `stdout.log`、`stderr.log`、`exit.json`、`diff.patch` を確認したか
- secret がログに含まれていないか
- 自動 commit / push / deploy が行われていないか

## Done Criteria

- 各 agent の担当範囲が明確
- Claude Code Agent 実行ログが保存されている
- diff を Leader が確認済み
- 残リスクが報告されている
- 自動 commit / push はしていない
