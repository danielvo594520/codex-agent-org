# codex-agent-org

Codex をリーダーとして、Codex subagent と Claude Code Agent を組織的に運用するための指示書リポジトリです。

## 目的

このリポジトリは、複数の AI エージェントを「組織」のように役割分担して使うための運用ルールをまとめます。

Codex は Leader として、目的整理、タスク分解、担当割り当て、成果物確認、最終判断を行います。
Codex subagent と Claude Code Agent は、調査・実装・レビュー・草案作成などの限定された担当者として使います。

## 基本思想

- Codex が Leader として最終責任を持つ
- エージェントは役割ごとに使い分ける
- すべての成果物は Codex Leader が検証してから採用する
- 自動採用・自動 push・自動 deploy はしない
- Claude Code Agent は強力な外部実行担当として扱う
- 破壊的操作や秘密情報を扱う作業は渡さない

## 運用モデル

- Leader: Codex 本体。要求整理、分解、統合、最終判断を行う。
- Explorer: 調査担当。コードベース、仕様、影響範囲を確認する。
- Worker: 実装担当。限定されたファイルや領域だけを変更する。
- Reviewer: レビュー担当。バグ、リグレッション、テスト不足を指摘する。
- Integrator: 統合担当。複数成果物の diff、衝突、検証を確認する。
- Claude Code Agent: Claude Code CLI 経由の外部担当。独立タスクの調査、草案、実装候補を作る。

詳細は [docs/organization-model.md](docs/organization-model.md) を参照してください。

## 最初の使い方

1. [AGENTS.md](AGENTS.md) を読む
2. 作業目的、制約、完了条件を Leader が整理する
3. [docs/handoff-contract.md](docs/handoff-contract.md) に沿って依頼文を作る
4. 必要に応じて Explorer / Worker / Reviewer / Integrator / Claude Code Agent に渡す
5. 成果物、diff、テスト結果、残リスクを Leader が確認する
6. 採用・修正・破棄を Leader が判断する

## Claude Code 呼び出し例

Claude Code Agent は原則として非対話実行します。

```bash
cd /path/to/worktree
claude -p \
  --permission-mode default \
  --output-format json \
  "$(cat tasks/task.md)"
```

推奨:

- 専用 worktree または一時ディレクトリで実行する
- 実行プロンプトを `tasks/*.md` として保存する
- 実行ごとに `runs/<timestamp>-<name>/` を作成する
- stdout / stderr / 終了コード / diff を保存する
- timeout を設定する
- 秘密情報を環境変数として渡さない
- 自動 commit / push / deploy はさせない

run log の標準手順は [docs/workflows/claude-code-run.md](docs/workflows/claude-code-run.md) を参照してください。

## Runner

`scripts/run-claude-agent` は、Claude Code Agent を呼び出し、prompt、実行コマンド、stdout、stderr、exit metadata、git diff を `runs/<timestamp>-<name>/` に保存する補助スクリプトです。

最小実行例:

```bash
scripts/run-claude-agent \
  --prompt tasks/claude-code-smoke-test.md \
  --cwd . \
  --name smoke-test \
  --timeout-sec 120
```

実行前に確認する場合:

```bash
scripts/run-claude-agent \
  --prompt tasks/claude-code-smoke-test.md \
  --cwd . \
  --name smoke-test \
  --dry-run
```

複数エージェント運用のリハーサルは [tasks/parallel-feature-smoke-test.md](tasks/parallel-feature-smoke-test.md) を使います。
runner はログ保存を補助するだけで、成果物の自動採用、自動 commit、自動 push、自動 deploy は行いません。

## 推奨ディレクトリ構成

```text
AGENTS.md
README.md
docs/
  organization-model.md
  handoff-contract.md
  safety-rules.md
  output-formats.md
  roles/
    leader.md
    explorer.md
    worker.md
    reviewer.md
    integrator.md
    claude-code-agent.md
  workflows/
    claude-code-run.md
    parallel-execution.md
    feature-development.md
    bugfix.md
    research.md
    review.md
  examples/
    feature-task.md
    bugfix-task.md
    code-review-task.md
    claude-code-handoff.md
tasks/
  complete-repository.md
  parallel-feature-smoke-test.md
runs/
  .gitkeep
scripts/
  README.md
  run-claude-agent
```

## まだ実装しないこと

このリポジトリは、まず運用ルールと指示書を整えることを目的にします。
以下は現時点では対象外です。

- 本格的な CLI / package 化
- Web UI
- 自動 commit
- 自動 push
- 自動 deploy
- GitHub Actions
- npm / Python package 化
- 複雑な設定ファイル

## 主要ドキュメント

- [docs/handoff-contract.md](docs/handoff-contract.md): エージェントへ作業を渡す標準フォーマット
- [docs/safety-rules.md](docs/safety-rules.md): 禁止事項と安全確認
- [docs/output-formats.md](docs/output-formats.md): 役割別の報告形式
- [docs/workflows/claude-code-run.md](docs/workflows/claude-code-run.md): Claude Code Agent 実行ログの保存手順
- [docs/workflows/parallel-execution.md](docs/workflows/parallel-execution.md): 並列実行の標準手順
- [docs/examples/](docs/examples/): コピペして使える依頼例
