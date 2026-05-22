# Add Runner and Parallel Smoke Test

あなたは Codex Leader です。
`codex-agent-org` に、Claude Code Agent 実行ログを自動保存する runner と、複数エージェント運用を試す smoke test 指示書を追加してください。

## Goal

以下の2つを同時に整備する。

1. Claude Code Agent を安全に呼び出し、実行ログを `runs/<timestamp>-<name>/` に保存する `scripts/run-claude-agent` を追加する
2. Codex Explorer / Worker / Reviewer / Claude Code Agent を組織的に動かすための smoke test 指示書を追加する

## Scope

作成・更新対象:

```text
scripts/run-claude-agent
tasks/parallel-feature-smoke-test.md
docs/workflows/claude-code-run.md
docs/workflows/parallel-execution.md
README.md
AGENTS.md
```

必要なら以下も追加してよい。

```text
scripts/README.md
```

## Out of Scope

今回は以下を実装しない。

- npm package 化
- Python package 化
- GitHub Actions
- Web UI
- 並列実行 scheduler
- 自動 commit
- 自動 push
- 自動 deploy
- Claude Code の成果物自動採用
- secret manager 連携

## Requirements

### scripts/run-claude-agent

Shell script でよい。

必須要件:

- `bash` で実行できる
- `set -u` と `set -o pipefail` を使う
- `--prompt <file>` を受け取る
- `--cwd <dir>` を受け取る
- `--name <name>` を任意で受け取る
- `--timeout-sec <seconds>` を任意で受け取る
- `--permission-mode <mode>` を任意で受け取る
- `--dry-run` を受け取る
- `claude` コマンドが存在しない場合は失敗する
- `--cwd` のディレクトリが存在しない場合は失敗する
- `--prompt` のファイルが存在しない場合は失敗する
- `claude` 側の `--cwd` オプションは使わない
- 実行時は `cd "$cwd"` してから `claude -p` を呼ぶ
- stdout を `stdout.log` に保存する
- stderr を `stderr.log` に保存する
- 実行コマンドを `command.txt` に保存する
- prompt を `prompt.md` にコピーする
- exit code、開始時刻、終了時刻、cwd、permission mode、timeout を `exit.json` に保存する
- 実行後に `git -C "$cwd" diff -- .` を `diff.patch` に保存する
- `runs/<timestamp>-<name>/` を作る
- script 自身は自動 commit / push しない
- permission mode のデフォルトは `default`
- timeout のデフォルトは `1800`
- name のデフォルトは `claude`
- dry-run では実行せず、作成予定の run directory と command を表示する

推奨:

- `timeout` コマンドがある場合は使う
- macOS で `gtimeout` がある場合は使う
- timeout コマンドがない場合は timeout なしで実行し、警告を stderr に出す
- run directory 名に使えない文字は `_` に置換する
- JSON は簡易生成でよいが、文字列の quote は壊さない

禁止:

- `eval` を使わない
- secret を表示しない
- `--dangerously-skip-permissions` をデフォルトにしない
- `git reset` しない
- `git checkout` しない
- 自動 commit / push しない

### tasks/parallel-feature-smoke-test.md

複数エージェント運用のリハーサル用タスクを書く。

内容:

- Codex Leader が最初に読む指示書
- Explorer / Worker / Reviewer / Claude Code Agent の役割を使う
- 実リポジトリを壊さない読み取り中心の smoke test にする
- Worker は小さな docs 追加または typo 修正程度に限定する
- Claude Code Agent には読み取り専用の調査タスクを渡す
- 最後に Leader が結果を統合し、ログと diff を確認する
- 自動 commit / push はしない

### docs/workflows/claude-code-run.md

`scripts/run-claude-agent` の使い方を追記する。

含める:

- 基本コマンド
- dry-run 例
- 出力される run directory
- 保存されるファイル
- Codex Leader が確認する順番
- secret を prompt に含めない注意

### docs/workflows/parallel-execution.md

実際に runner を使って Claude Code Agent を呼ぶ例を追記する。

### README.md

以下を追記する。

- `scripts/run-claude-agent` の概要
- 最小実行例
- parallel smoke test の場所
- まだ自動採用はしない方針

### AGENTS.md

必要なら以下を補強する。

- Claude Code Agent の実行は runner 経由を推奨
- run log を Leader が確認してから採用判断する

## Implementation Notes

Shell script は読みやすさを優先する。
過剰な汎用化はしない。

想定コマンド:

```bash
scripts/run-claude-agent \
  --prompt tasks/claude-code-smoke-test.md \
  --cwd /Users/miyazawa/workspace/codex-agent-org \
  --name smoke-test \
  --timeout-sec 600
```

dry-run:

```bash
scripts/run-claude-agent \
  --prompt tasks/claude-code-smoke-test.md \
  --cwd /Users/miyazawa/workspace/codex-agent-org \
  --name smoke-test \
  --dry-run
```

## Verification

以下を実行する。

```bash
bash -n scripts/run-claude-agent
scripts/run-claude-agent --dry-run --prompt tasks/claude-code-smoke-test.md --cwd . --name smoke-test
find . -maxdepth 4 -type f | sort
git diff --check
```

可能なら、読み取り専用の短い prompt で実行確認する。

```bash
scripts/run-claude-agent --prompt tasks/claude-code-smoke-test.md --cwd . --name smoke-test --timeout-sec 120
```

実行した場合は、生成された `runs/<timestamp>-smoke-test/` の内容を確認する。

```bash
find runs -maxdepth 2 -type f | sort
```

## Final Report

最後に以下の形式で報告する。

```md
完了しました。

作成・更新ファイル:
- ...

確認:
- ...

生成された run log:
- ...

残リスク:
- ...

次にやるなら:
- ...
```
