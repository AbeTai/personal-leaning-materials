# 学習資料自動生成システム

URLやPDFファイルから、学習用の静的HTML/CSS教材を生成し、GitHub Pagesで公開するためのリポジトリです。Claude Codeの`learning-material-generator` skillを使い、資料取得、分析、ユーザ診断、教材生成、Git管理までを行います。

## 目次

- [機能概要](#機能概要)
- [ディレクトリ構造](#ディレクトリ構造)
- [セットアップ](#セットアップ)
- [使い方](#使い方)
- [ユーザ知識メモリ](#ユーザ知識メモリ)
- [GitHub Pages公開](#github-pages公開)
- [トラブルシューティング](#トラブルシューティング)

---

## 機能概要

このシステムは以下の処理を自動で行います。

### 1. 資料取得・分析

- URLまたはPDFファイルから内容を取得
- 資料ごとに難易度、信頼度、前提知識を分析
- 複数資料がある場合は、重複、矛盾、推奨読解順を整理

### 2. ユーザ診断

- ユーザの現在の知識レベルを選択肢形式で確認
- 学習目的、対象読者、時間制約、重点領域を把握
- 過去の学習履歴を`user-memory/`から読み込み、質問と教材内容を調整

### 3. HTML/CSS教材生成

- ユーザの理解レベルに合わせた学習ロードマップを作成
- 読み物として使える縦長のHTML教材ページを生成
- 各概念に要点、具体例、理解チェック、次のステップを含める
- CSSはモバイルとデスクトップの両方で読みやすい構成にする

### 4. Git管理 + GitHub Pages公開

- 生成した教材を`docs/`配下に配置
- 成果物をGitHubリポジトリにコミット、プッシュ
- GitHub Pagesの`main`ブランチ`/docs`配信で無料公開

---

## ディレクトリ構造

```
personal-leaning-materials/
├── README.md
├── .gitignore
├── learning-material-generator.skill
├── user-memory/
│   ├── .gitkeep
│   ├── database.md
│   ├── frontend.md
│   ├── backend.md
│   └── ...
├── docs/
│   ├── index.html
│   ├── styles.css
│   └── YYYY-MM-DD_トピック名/
│       ├── index.html
│       └── styles.css
├── YYYY-MM-DD_トピック名/
│   ├── sources/
│   │   ├── doc_1_xxx.md
│   │   └── original.pdf
│   └── output/
│       └── generation_notes.md
└── workspace/
    └── YYYY-MM-DD_トピック名/
        └── ...
```

| ディレクトリ | Git管理 | 用途 |
| --- | --- | --- |
| `docs/` | あり | GitHub Pagesで公開するHTML/CSS教材 |
| `user-memory/` | あり | ユーザの知識・学習履歴 |
| `YYYY-MM-DD_トピック名/` | あり | 元資料と生成メモ |
| `workspace/` | なし | 作業用の一時ファイル |

---

## セットアップ

### 前提条件

- Claude Code
- Git
- GitHubアカウント
- GitHub Pagesを有効化できるリポジトリ

### リポジトリを使う

```bash
git clone https://github.com/<YOUR_USERNAME>/personal-leaning-materials.git
cd personal-leaning-materials
```

新規に作る場合は、GitHubで空リポジトリを作成してから以下を実行します。

```bash
mkdir personal-leaning-materials
cd personal-leaning-materials
git init
git remote add origin https://github.com/<YOUR_USERNAME>/personal-leaning-materials.git
mkdir -p user-memory docs
```

`.gitignore`には以下を含めます。

```gitignore
workspace/
.DS_Store
node_modules/
```

`docs/`はGitHub Pagesで公開するため、Git管理対象にします。

---

## 使い方

プロジェクトディレクトリでClaude Codeを起動します。

```bash
cd personal-leaning-materials
claude
```

チャットで資料を渡します。

```text
この資料から学習資料を作って：
https://example.com/article1
https://example.com/article2
```

PDFファイルを渡す場合:

```text
このPDFから学習資料を作って：
/path/to/document.pdf
```

### 実行フロー

1. 資料を取得し、`YYYY-MM-DD_トピック名/sources/`に保存
2. 資料ごとに要約、重要概念、前提知識、難易度、信頼度を分析
3. 複数資料の場合は、概念マップ、重複、矛盾、推奨読解順を整理
4. 選択肢形式で3〜4問のユーザ診断を実施
5. 回答内容を`user-memory/`に追記・更新
6. 学習ロードマップと教材構成を作成
7. `docs/YYYY-MM-DD_トピック名/index.html`と`styles.css`を生成
8. `docs/index.html`の教材一覧を更新
9. 静的チェック後、GitHubへコミット・プッシュ
10. GitHub Pagesの公開URLを提示

### 生成されるファイル

```
YYYY-MM-DD_トピック名/
├── sources/
│   └── doc_1_xxx.md
└── output/
    └── generation_notes.md

docs/
├── index.html
├── styles.css
└── YYYY-MM-DD_トピック名/
    ├── index.html
    └── styles.css
```

---

## ユーザ知識メモリ

`user-memory/`には、ユーザの既存知識と学習履歴をジャンル別に記録します。

例:

```markdown
# Database 関連の知識

## 理解している概念・技術

### Snowflake
- **理解レベル**: 初級（名前だけ知っている）
- **詳細**: クラスタリングキーや検索最適化の存在は知っているが、使ったことはない
- **最終更新**: 2026-04-27

## 学習済みトピック

- Snowflakeクラスタリングと検索最適化 (2026-04-27)
```

この情報は、次回以降の質問や教材の深さを調整するために使います。

---

## GitHub Pages公開

このリポジトリでは、GitHub Pagesの公開元を`main`ブランチの`/docs`に設定します。

### 初回設定

1. GitHubで対象リポジトリを開く
2. `Settings`を開く
3. `Pages`を開く
4. `Build and deployment`で`Deploy from a branch`を選ぶ
5. Branchを`main`、Folderを`/docs`に設定する
6. `Save`する

公開URLの基本形:

```text
https://abetai.github.io/personal-leaning-materials/
```

教材ごとのURL:

```text
https://abetai.github.io/personal-leaning-materials/YYYY-MM-DD_トピック名/
```

ユーザ名やリポジトリ名が異なる場合は、URLもそれに合わせて変わります。

---

## トラブルシューティング

### GitHub Pagesに反映されない

- GitHub Pagesの公開元が`main` / `/docs`になっているか確認
- `docs/index.html`がGitHubにpushされているか確認
- Pagesの反映には数十秒から数分かかる場合があります

### 教材ページのリンクが切れている

- `docs/index.html`から各教材へのリンクが`./YYYY-MM-DD_トピック名/`形式になっているか確認
- 各教材ディレクトリに`index.html`があるか確認

### workspaceディレクトリがGitにコミットされる

`.gitignore`に`workspace/`が含まれているか確認します。

```bash
cat .gitignore
```

既にGit管理に入っている場合は、キャッシュから外します。

```bash
git rm -r --cached workspace/
git commit -m "workspaceをGit管理から除外"
```

### git pushが失敗する

SSH設定またはHTTPS認証を確認します。

```bash
git remote -v
ssh -T git@github.com
```

---

## 補足

- 教材は通常のHTML/CSSとして生成するため、プレゼン用ファイルや変換ファイルは生成しません。
- 外部ストレージ同期は行いません。
- ブラウザ起動は行わず、ファイル存在、相対リンク、主要セクション、旧成果物名の混入なしを静的に確認します。
