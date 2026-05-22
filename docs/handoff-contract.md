# Handoff Contract

エージェントに作業を渡す時の標準フォーマットです。
Leader はこの形式で目的、範囲、禁止事項、期待出力を明確にしてから依頼します。

## 標準フォーマット

```md
# Task

## Goal

何を達成するか。

## Context

前提、関連 issue、関連ファイル、既存調査結果。

## Scope

今回扱う作業範囲。

## Files You Own

編集または調査してよいファイル。

## Files You Must Not Touch

触ってはいけないファイル。

## Constraints

- 他のエージェントの変更を revert しない
- 担当範囲外のリファクタをしない
- 自動 commit / push / deploy をしない
- 不明点は仮定として明記する

## Expected Output

- 要約
- 変更ファイル
- 確認結果
- 残リスク

## Verification

実行してほしい確認コマンド、または確認観点。

## Final Report Format

最後に返してほしい形式。
```

## Worker 向け例

````md
# Task

## Goal

ログイン画面の入力バリデーションを追加する。

## Context

既存のフォーム実装は `src/login` 配下にある。
エラーメッセージの表示パターンは `src/shared/form` を参照する。

## Scope

メールアドレス未入力、形式不正、パスワード未入力の検証を追加する。

## Files You Own

- `src/login/LoginForm.tsx`
- `src/login/LoginForm.test.tsx`

## Files You Must Not Touch

- `src/auth/*`
- `src/shared/api/*`

## Constraints

- 他のエージェントの変更を revert しない
- API 仕様は変更しない
- UI 文言は既存パターンに合わせる

## Expected Output

- 実施内容
- 変更ファイル
- テスト結果
- 残リスク

## Verification

```bash
npm test -- LoginForm
```

## Final Report Format

```md
完了しました。

変更:
- ...

確認:
- ...

残リスク:
- ...
```
````

## Claude Code Agent 向け追加項目

Claude Code Agent には、標準フォーマットに加えて以下を明記します。

- Working Directory
- Allowed Files
- Forbidden Actions
- Timeout
- Expected Final Report

```md
# Claude Code Agent Task

あなたは Codex Leader の外部実行エージェントです。
指定された作業だけを行ってください。

## Goal

...

## Working Directory

...

## Allowed Files

...

## Forbidden Actions

- git push
- git reset --hard
- deploy
- secret の表示
- 担当外ファイルの編集

## Task

...

## Expected Final Report

- 実施内容
- 変更ファイル
- テスト結果
- 判断に迷った点
- Codex Leader が確認すべき点
```

## Leader の確認

結果を受け取った Leader は、採用前に必ず以下を確認します。

- `git status`
- 変更ファイル
- 想定外ファイルの有無
- diff の内容
- テスト結果
- 残リスク
