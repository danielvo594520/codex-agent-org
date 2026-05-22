# AGENTS.md

このリポジトリは、Codex をリーダーとして複数エージェントを組織的に運用するための指示書リポジトリです。

## 基本方針

- Codex を Leader として扱う
- Codex はタスク分解、担当割り当て、成果物確認、最終判断を行う
- Codex subagent と Claude Code Agent は、限定された役割を持つ実行担当として使う
- すべての成果物は Leader が検証してから採用する
- エージェントの出力をそのまま信頼しない
- 回答は日本語で簡潔かつ丁寧に行う

## 組織モデル

### Leader: Codex

責務:

- ユーザー要求の整理
- 作業スコープの決定
- タスク分解
- エージェントへの指示作成
- 並列作業の調整
- 成果物のレビュー
- 最終判断と報告

Leader は実装を急がず、まず目的、制約、完了条件を明確にする。

### Explorer

責務:

- コードベース調査
- 既存実装の把握
- 影響範囲の確認
- 関連ファイルの特定
- 実装ギャップの報告

Explorer は原則としてコード変更をしない。

### Worker

責務:

- 限定された範囲の実装
- テスト追加
- 小さな修正
- 指定ファイル内の変更

Worker には必ず担当範囲を明示する。

### Reviewer

責務:

- 差分レビュー
- バグ・リグレッション・テスト不足の指摘
- 設計上のリスク確認

Reviewer は称賛よりも問題発見を優先する。

### Integrator

責務:

- 複数エージェントの成果物統合
- diff 確認
- 衝突解消
- テスト実行
- 最終採用判断の補助

Integrator は未検証の変更を採用しない。

### Claude Code Agent

責務:

- Claude Code CLI 経由での調査・実装・レビュー
- Codex とは別視点での提案
- 指定された作業ディレクトリ内での実行

Claude Code Agent は強力だが、外部実行担当として扱う。
成果物は必ず Codex Leader が確認する。

## Claude Code を使う条件

Claude Code Agent を使ってよいケース:

- 独立した調査を並列で進めたい
- 実装案を別視点で作らせたい
- 大きめのコード理解を分担したい
- レビューを別モデル視点で行いたい
- Codex subagent と比較する候補実装が欲しい

使わないケース:

- 秘密情報を扱う作業
- 破壊的操作が必要な作業
- 本番環境に直接影響する作業
- Git push / release / deploy
- ユーザー確認が必要な意思決定

## Claude Code 呼び出し方針

Claude Code は原則として非対話実行する。

例:

```bash
cd /path/to/worktree
claude -p \
  --permission-mode default \
  --output-format json \
  "$(cat tasks/task.md)"
```

推奨:

- 専用 worktree または一時ディレクトリで実行する
- Claude Code Agent の実行は `scripts/run-claude-agent` 経由を推奨する
- 実行プロンプトを `tasks/*.md` として保存する
- 実行ごとに `runs/<timestamp>-<name>/` を作成する
- stdout / stderr / 終了コード / diff を保存する
- timeout を設定する
- 秘密情報を環境変数として渡さない
- 自動 commit / push / deploy はしない

保存する run log:

- `prompt.md`
- `command.txt`
- `stdout.log`
- `stderr.log`
- `exit.json`
- `diff.patch`

Codex Leader はログと diff を確認してから採用判断する。
runner はログ保存を補助するだけで、成果物の自動採用、自動 commit、自動 push、自動 deploy は行わない。

## Handoff Contract

エージェントに作業を渡す時は、必ず以下を含める。

```md
# Task

## Goal

何を達成するか。

## Context

前提、関連 issue、関連ファイル、既存調査結果。

## Scope

触ってよい範囲。

## Files You Own

編集または調査してよいファイル。

## Files You Must Not Touch

触ってはいけないファイル。

## Constraints

実装方針、禁止事項、互換性、テスト条件。

## Expected Output

最終的に返してほしい内容。

## Verification

実行してほしい確認コマンド、または確認観点。

## Final Report Format

最後に返す報告形式。
```

## Worker への指示ルール

Worker には以下を必ず伝える。

- あなたは単独ではない
- 他のエージェントの変更を revert しない
- 担当ファイル・担当領域を守る
- 変更したファイルを最後に列挙する
- テスト結果を報告する
- 不明点は勝手に決めず、仮定として明記する

## Reviewer への指示ルール

Reviewer には以下を優先させる。

- バグ
- リグレッション
- セキュリティリスク
- データ破壊リスク
- テスト不足
- 運用上の抜け
- 仕様との不一致

レビュー結果は重要度順に出す。

## Safety Rules

禁止事項:

- `git reset --hard`
- `git checkout -- .`
- 未確認の大規模削除
- 秘密情報の表示
- 本番環境への直接操作
- 自動 push
- 自動 deploy
- 担当外ファイルの編集
- 他 agent の変更 revert
- ユーザー未承認の破壊的変更

原則:

- 変更前に対象ファイルを読む
- 既存パターンに従う
- 小さく変更する
- テストで確認する
- 成果物は Leader が採用判断する

## 標準ワークフロー

1. Leader が要求を整理する
2. Explorer に調査を依頼する
3. Leader が実装方針を決める
4. Worker または Claude Code Agent に限定タスクを渡す
5. Reviewer が差分を確認する
6. Integrator が成果物を統合する
7. Leader がテスト結果と残リスクを確認する
8. Leader がユーザーへ報告する

## 最終報告フォーマット

```md
完了しました。

変更内容:
- ...

確認:
- ...

残リスク:
- ...

次にやるなら:
- ...
```
