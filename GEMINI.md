# プロジェクト概要及び開発ルール (Git Subtree 運用)

本リポジトリ (`test-master`) は、フロントエンドとバックエンドを統合し、LLM（生成AIエージェント）による開発をスムーズに行うためのモノレポ（統合リポジトリ）です。
既存の資産・履歴を安全に保ちつつ、フロントエンドとバックエンドを横断した統合環境で開発を進めるために **Git Subtree** を採用しています。

## リポジトリ構成

- **統合リポジトリ (親)**: `https://github.com/shun/test-master`
- **フロントエンド (Subtree)**: `https://github.com/shun/test-frontend` -> 本リポジトリの `frontend/` ディレクトリ配下に展開
- **バックエンド (Subtree)**: `https://github.com/shun/test-backend` -> 本リポジトリの `backend/` ディレクトリ配下に展開

---

## LLM（AIコーディングエージェント）への指示・開発ルール

### 1. 基本となる開発フロー
- ファイルの編集、追加、削除はすべてこの統合リポジトリ (`test-master`) 内で通常通り行ってください。
- git の操作（`add` や `commit`）も、ルートディレクトリで通常通り行います。
- **フロントエンドとバックエンドにまたがる変更（APIのレスポンス変更と、それを受け取るUIの変更など）も、1つのコミットにまとめて問題ありません。**
- `frontend` や `backend` ディレクトリへ `cd` して個別の Git リポジトリとして操作する必要はありません（Submodule ではないため）。

### 2. Git Subtree を用いた同期コマンド
統合リポジトリ内の変更を元リポジトリに戻したり、元リポジトリの最新の変更を取り込んだりするには、以下の `git subtree` コマンドを使用します。
（前提として、リモートリポジトリ `frontend` および `backend` が登録されているものとします）

#### 👉 最新の変更を取り込む (Pull)
それぞれの元のリポジトリで別の開発者が行った変更を、この統合リポジトリに取り込む場合：
```bash
# フロントエンドの最新を取り込む
git subtree pull --prefix=frontend frontend main --squash

# バックエンドの最新を取り込む
git subtree pull --prefix=backend backend main --squash
```
※ `--squash` をつけることで、相手側の詳細なコミット履歴を1つにまとめて取り込み、統合リポジトリの履歴が肥大化するのを防ぎます。

#### 👉 開発した変更を還元する (Push / Backport)
この統合リポジトリで開発した内容を、それぞれの元のリポジトリに切り出してプッシュする場合：
```bash
# フロントエンドの変更箇所だけを抽出してPush
git subtree push --prefix=frontend frontend main

# バックエンドの変更箇所だけを抽出してPush
git subtree push --prefix=backend backend main
```

### 3. 注意事項
- **クリーンな状態で実行すること**: `git subtree pull` や `git subtree push` を実行する前には、必ず現在の変更をすべてコミットし、ワーキングツリーをクリーンな状態にしてください。
- リモートが未登録の場合は、以下のコマンドで登録してから subtree コマンドを使用してください。
  ```bash
  git remote add frontend https://github.com/shun/test-frontend.git
  git remote add backend https://github.com/shun/test-backend.git
  git fetch frontend
  git fetch backend
  ```
