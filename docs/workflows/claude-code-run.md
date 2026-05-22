# Claude Code Run Workflow

Claude Code Agent を実行するときに、prompt、stdout、stderr、exit code、git diff を保存するための標準手順です。

## When to use

- Claude Code Agent に調査、草案作成、実装候補、レビューを依頼する
- 実行結果を後から追跡したい
- 複数 agent の成果物を Codex Leader が比較・検証したい

使わないケース:

- secret や本番データを prompt に含む
- 本番環境操作が必要
- ユーザー確認が必要な意思決定を agent に任せる

## Run log directory

実行ごとに以下のディレクトリを作成します。

```text
runs/<timestamp>-claude/
```

例:

```text
runs/20260522-143000-claude/
```

保存するファイル:

- `prompt.md`: Claude Code Agent に渡した prompt
- `command.txt`: 実行コマンド
- `stdout.log`: 標準出力
- `stderr.log`: 標準エラー
- `exit.json`: exit code と実行時刻
- `diff.patch`: 実行後の `git diff`

## Steps

1. Codex Leader が Handoff Contract に沿って prompt を作る
2. secret や本番データが prompt に含まれていないことを確認する
3. `runs/<timestamp>-claude/` を作成する
4. prompt を `prompt.md` に保存する
5. `cd /path/to/worktree` してから `claude -p` を実行する
6. stdout / stderr / exit code を保存する
7. 実行後に `git diff` を `diff.patch` に保存する
8. Codex Leader がログ、diff、変更ファイルを確認する
9. 採用、修正、破棄を Codex Leader が判断する

## Command example

`--cwd` は使いません。
作業ディレクトリへ移動してから `claude -p` を実行します。

```bash
cd /path/to/worktree
timestamp="$(date +%Y%m%d-%H%M%S)"
run_dir="runs/${timestamp}-claude"
mkdir -p "$run_dir"

cp tasks/claude-code-smoke-test.md "$run_dir/prompt.md"

cat > "$run_dir/command.txt" <<EOF
cd /path/to/worktree
claude -p --permission-mode default --output-format json "\$(cat $run_dir/prompt.md)"
EOF

set +e
claude -p \
  --permission-mode default \
  --output-format json \
  "$(cat "$run_dir/prompt.md")" \
  > "$run_dir/stdout.log" \
  2> "$run_dir/stderr.log"
exit_code=$?
set -e

printf '{"exit_code":%s,"timestamp":"%s"}\n' "$exit_code" "$(date -Iseconds)" > "$run_dir/exit.json"
git diff > "$run_dir/diff.patch"
```

読み取り専用にしたい場合は、利用中の Claude Code CLI の対応状況に合わせて `--allowedTools` と `--disallowedTools` を追加します。

例:

```bash
claude -p \
  --permission-mode default \
  --output-format json \
  --allowedTools Read \
  --disallowedTools Edit,Write,MultiEdit,Bash \
  "$(cat "$run_dir/prompt.md")"
```

## Safety rules

- secret を prompt、stdout、stderr、diff に保存しない
- secret が出力された可能性がある場合は、ログを共有・commit しない
- 自動 commit / push / deploy はしない
- `git reset --hard` や `git checkout -- .` は使わない
- 担当外ファイルの変更を採用しない
- Claude Code Agent の成果物は必ず Codex Leader が確認する

## Verification

Codex Leader は実行後に以下を確認します。

```bash
git status
git diff --stat
cat runs/<timestamp>-claude/exit.json
test -s runs/<timestamp>-claude/prompt.md
test -f runs/<timestamp>-claude/stdout.log
test -f runs/<timestamp>-claude/stderr.log
test -f runs/<timestamp>-claude/diff.patch
```

## Done criteria

- run log directory が作成されている
- `prompt.md`、`command.txt`、`stdout.log`、`stderr.log`、`exit.json`、`diff.patch` が保存されている
- exit code が確認されている
- diff を Codex Leader が読んでいる
- secret が保存されていない
- 自動 commit / push / deploy をしていない
