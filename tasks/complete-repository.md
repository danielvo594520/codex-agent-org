# Complete Repository Task

あなたは Codex Leader です。
このリポジトリ `codex-agent-org` を、Codex をリーダーにした複数エージェント組織運用の指示書リポジトリとして完成させてください。

## Goal

Codex subagent と Claude Code Agent を組織的に使うための、実用可能なドキュメント一式を整備する。

このリポジトリは実行CLIの実装ではなく、まず運用ルール・役割定義・並列実行フロー・handoff contract・安全ルールを完成させる。

## Scope

以下のファイルとディレクトリを作成・整備する。

```text
README.md
AGENTS.md
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
runs/
  .gitkeep
```

## Out of Scope

今回は以下を実装しない。

- Claude Code 呼び出しCLI
- Web UI
- 自動 commit
- 自動 push
- 自動 deploy
- GitHub Actions
- npm / Python package 化
- 複雑な設定ファイル

## Required Content

### README.md

以下を説明する。

- リポジトリの目的
- Codex Leader を中心にした運用モデル
- Codex subagent と Claude Code Agent の使い分け
- 推奨ディレクトリ構成
- 最初の使い方
- まだ実装しないこと

### AGENTS.md

既存内容を維持しつつ、必要なら整える。

必ず含める。

- 日本語で簡潔かつ丁寧に回答する
- Codex は Leader
- Explorer / Worker / Reviewer / Integrator / Claude Code Agent の役割
- Claude Code の成果物は Codex が検証して採用する
- 破壊的操作は禁止
- 自動 push / deploy は禁止

### docs/organization-model.md

組織モデルを書く。

- Leader
- Explorer
- Worker
- Reviewer
- Integrator
- Claude Code Agent

それぞれについて以下を書く。

- 責務
- 任せてよい作業
- 任せてはいけない作業
- 期待する出力

### docs/handoff-contract.md

エージェントへ作業を渡す標準フォーマットを書く。

必ず以下を含める。

- Goal
- Context
- Scope
- Files You Own
- Files You Must Not Touch
- Constraints
- Expected Output
- Verification
- Final Report Format

### docs/safety-rules.md

安全ルールを書く。

必ず禁止事項を含める。

- git reset --hard
- git checkout -- .
- secret の表示
- 本番環境操作
- 自動 push
- 自動 deploy
- 担当外ファイルの編集
- 他 agent の変更 revert

### docs/output-formats.md

各 agent の報告フォーマットを書く。

- Explorer report
- Worker report
- Reviewer report
- Integrator report
- Claude Code Agent report
- Leader final report

### docs/roles/*.md

各 role ごとに、単独で読める指示書を書く。

各ファイルに含める。

- Role
- Mission
- Responsibilities
- Allowed Actions
- Forbidden Actions
- Input Format
- Output Format

### docs/workflows/*.md

各 workflow を書く。

- `parallel-execution.md`: 複数 agent を一気に動かす標準手順
- `feature-development.md`: 機能開発時の流れ
- `bugfix.md`: バグ修正時の流れ
- `research.md`: 調査タスクの流れ
- `review.md`: レビュータスクの流れ

各 workflow は以下を含める。

- When to use
- Steps
- Agents to use
- Handoff examples
- Verification
- Done criteria

### docs/examples/*.md

実際にコピペして使える例を書く。

- feature task
- bugfix task
- code review task
- Claude Code handoff task

## Writing Style

- 日本語で書く
- 簡潔にする
- 実務でそのまま使える粒度にする
- 抽象論だけにしない
- 各ドキュメントは単独で読めるようにする
- 重複は許容するが、矛盾は作らない

## Verification

作業後に以下を確認する。

```bash
find . -maxdepth 4 -type f | sort
```

さらに以下を確認する。

- 必須ファイルがすべて存在する
- `README.md` と `AGENTS.md` の方針が矛盾していない
- Claude Code Agent の扱いが安全側になっている
- 自動 commit / push / deploy を推奨していない
- 並列実行時の担当範囲が明確になっている

## Final Report

最後に以下の形式で報告する。

```md
完了しました。

作成・更新したファイル:
- ...

確認:
- ...

残リスク:
- ...

次にやるなら:
- ...
```
