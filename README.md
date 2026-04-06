# gitc

`git` コマンドの「よく使う操作」を短い引数で呼べるようにした、個人用ショートカット CLI です。

- 実体は PHP の 1 ファイル `gitc` です
- `git` のラッパーなので、`git` が使えるリポジトリ配下で実行してください

## 動作環境

- PHP (CLI)
- Git

## セットアップ

```bash
# 実行権限付与
chmod +x gitc

# 例: PATH の通った場所へ置く（任意）
# sudo cp gitc /usr/local/bin/gitc
```

## 使い方

```bash
gitc -h
```

引数（サブコマンド）は **すべて `-` 付き**です。

- 例: `gitc -st` / `gitc -pl` / `gitc -sw main`

## コマンド一覧

### 基本

- `gitc -st`
  - `git status -sb`
- `gitc -pl`
  - `git pull --rebase`
- `gitc -ps`
  - `git push`
- `gitc -d`
  - `git diff`
- `gitc -ds`
  - `git diff --staged`
- `gitc -lg`
  - `git log --oneline --graph --decorate --all`
- `gitc -br`
  - `git branch`

### ブランチ

- `gitc -sw <branch>`
  - `git switch <branch>`
  - ブランチが存在しない場合は **作成しません**（`-new` を案内して終了）
- `gitc -new <branch>`
  - `git switch -c <branch>`

### stash

- `gitc -ss [message]`
  - `git stash push -m <message>`
  - `message` 省略時は `wip`
- `gitc -sp`
  - `git stash pop`

### リポジトリ整理/同期

- `gitc -sync`
  - `git fetch --prune`
- `gitc -tidy`
  - `git branch --merged`

### サマリー表示

- `gitc -status`
  - 現在のブランチ
  - 最新コミット（1行）
  - upstream との差分（ahead/behind）
  - 作業ツリーの状態（dirty/clean）
  - その後に `git status -sb`

## 複合コマンド

- `gitc -acp "コミットコメント"`
  - `git add -A`
  - `git commit -m "..."`
  - `git push`

## 危険操作（要確認）

### reset

- `gitc -rs <reset args...>`
  - 例: `gitc -rs --soft HEAD~1`
  - 実行前に確認プロンプトが出ます

### undo

- `gitc -undo`
  - 状況に応じて以下を行います（どちらも要確認）

1. 変更がある場合（staged/unstaged を含む）

- `git restore --staged .`
- `git restore .`

2. 変更が無い場合

- `git reset --soft HEAD~1`

## 救出（rescue）

- `gitc -rescue`
  - 直近の `reflog` を表示します（`-n 20`）
- `gitc -rescue HEAD@{n}`
- `gitc -rescue <hash>`
  - `rescue-YYYYmmdd-HHMMSS` ブランチを作成して移動します

## 備考

- コマンドは内部的に `passthru()` で実行します
- `commit` メッセージ等は `escapeshellarg()` でエスケープしています
