# 学習資料自動生成システム

URLやPDFファイルから、自動的に学習用のPowerPointスライド（PPTX）とPDFを生成するシステムです。Claude Codeの`learning-material-generator` skillを使用して、資料の分析からスライド生成、Git管理、Google Drive同期まで全自動で行います。

## 📋 目次

- [機能概要](#機能概要)
- [ディレクトリ構造](#ディレクトリ構造)
- [セットアップ](#セットアップ)
- [使い方](#使い方)
- [ユーザ知識メモリ](#ユーザ知識メモリ)
- [Google Drive自動同期](#google-drive自動同期)
- [トラブルシューティング](#トラブルシューティング)

---

## 🚀 機能概要

このシステムは以下の処理を自動で行います：

### 1. **資料取得・分析**
- URLまたはPDFファイルから内容を自動取得
- 資料ごとに難易度・信頼度・前提知識を分析
- 複数資料がある場合は統合分析（矛盾・重複の検出、推奨読解順の提示）

### 2. **ユーザ診断**
- ユーザの現在の知識レベルを質問形式で確認
- 学習目的・対象読者・時間制約などを把握
- 過去の学習履歴（`user-memory/`）を参考に質問を最適化

### 3. **学習資料生成**
- ユーザの理解レベルに合わせた学習ロードマップを作成
- 1概念1スライドの原則で、具体例・図解を含むスライドを生成
- PPTXとPDFの両方を自動生成

### 4. **Git管理 + Google Drive同期**
- 成果物を自動的にGitHubリポジトリにコミット・プッシュ
- Google Apps Script (GAS) が定期的にGitHubを監視し、自動的にGoogle Driveに同期

---

## 📁 ディレクトリ構造

```
personal-leaning-materials/          ← プロジェクトルート
│
├── README.md                        ← このファイル
├── .gitignore                       ← workspace/ を除外
├── learning-material-generator.skill ← Claude Code skill（ZIPファイル）
│
├── user-memory/                     ← ユーザの知識を記録（Git管理対象）
│   ├── .gitkeep
│   ├── database.md                 ← データベース関連の知識
│   ├── frontend.md                 ← フロントエンド関連の知識
│   ├── backend.md                  ← バックエンド関連の知識
│   ├── infrastructure.md           ← インフラ・クラウド関連の知識
│   └── ...                         ← その他、ジャンル別に追加
│
├── YYYY-MM-DD_トピック名/          ← 学習資料ごとのディレクトリ（Git管理対象）
│   ├── sources/                    ← 取得した元資料
│   │   ├── doc_1_xxx.md
│   │   └── original.pdf
│   └── output/                     ← 生成されたスライド
│       ├── learning_slides.pptx
│       └── learning_slides.pdf
│
└── workspace/                       ← 作業用ディレクトリ（Git管理外）
    └── YYYY-MM-DD_トピック名/      ← 資料群ごとに独立した作業領域
        ├── slides/                 ← HTMLスライド（中間生成物）
        ├── node_modules/           ← npm依存関係
        ├── package.json
        ├── build-pptx.js           ← PPTX生成スクリプト
        ├── output.pptx             ← 中間生成物
        └── thumbnails/             ← サムネイル（品質確認用）
```

### ディレクトリの役割

| ディレクトリ | Git管理 | 用途 |
|------------|--------|------|
| `user-memory/` | ✅ あり | ユーザの知識・学習履歴を記録 |
| `YYYY-MM-DD_トピック名/` | ✅ あり | 学習資料の成果物（sources, output） |
| `workspace/` | ❌ なし | 作業用の一時ファイル（node_modules等） |

---

## 🛠️ セットアップ

### 前提条件

以下のツールが必要です：

1. **Claude Code CLI**
   - [公式サイト](https://claude.ai/code)からインストール

2. **Node.js & npm**
   ```bash
   brew install node
   ```

3. **LibreOffice**（PPTX → PDF変換用）
   ```bash
   brew install --cask libreoffice
   ```

4. **Git & GitHub**
   - GitHubアカウントを作成
   - リポジトリを作成（例: `personal-leaning-materials`）
   - ローカルでcloneまたは初期化

### 初回セットアップ手順

#### 1. リポジトリのクローンまたは初期化

```bash
# 新規作成の場合
mkdir personal-leaning-materials
cd personal-leaning-materials
git init
git remote add origin <YOUR_GITHUB_REPO_URL>

# 既存リポジトリをcloneする場合
git clone <YOUR_GITHUB_REPO_URL>
cd personal-leaning-materials
```

#### 2. 必要なディレクトリの作成

```bash
mkdir -p user-memory
touch user-memory/.gitkeep
```

#### 3. .gitignore の設定

プロジェクトルートに `.gitignore` を作成：

```bash
cat > .gitignore << 'EOF'
# 学習資料生成スキル用の作業ディレクトリ（資料群ごとに作成される）
workspace/

# macOS
.DS_Store

# Node
node_modules/
EOF
```

#### 4. Claude Code skillのインストール

`learning-material-generator.skill` ファイルをClaude Codeのskillsディレクトリにコピー：

```bash
# skillファイルをClaude Codeに認識させる
# （このリポジトリにlearning-material-generator.skillが含まれている場合）
# Claude Codeを起動すれば自動的に認識されます
```

#### 5. 初回コミット

```bash
git add .
git commit -m "初回コミット: 学習資料生成システムのセットアップ"
git push -u origin main
```

---

## 💡 使い方

### 基本的な使い方

Claude Codeを起動し、以下のように資料を渡すだけです：

```
この資料からスライドを作って：
https://example.com/article1
https://example.com/article2
```

または、PDFファイルを直接渡すこともできます：

```
このPDFから学習資料を作って：
/path/to/document.pdf
```

### 実行フロー

1. **資料取得**
   - URLやPDFから内容を自動取得
   - `YYYY-MM-DD_トピック名/sources/` に保存

2. **資料分析**
   - 各資料の難易度・信頼度・前提知識を分析
   - 複数資料がある場合は統合分析

3. **ユーザ診断**（ここで一時停止）
   - 選択肢形式で3〜4問を質問
   - 学習目的、現在の知識レベル、時間制約などを確認
   - `user-memory/` の過去の学習履歴も参考にして質問を最適化

4. **スライド生成**
   - 回答をもとに、ユーザに最適な学習ロードマップを作成
   - PPTXとPDFを生成（`YYYY-MM-DD_トピック名/output/`）

5. **Git管理**
   - 成果物を自動的にGitHubにコミット・プッシュ
   - コミットメッセージ: `「トピック名」の学習資料を追加`

6. **Google Drive同期**
   - GASが定期的にGitHubを監視し、自動的にGoogle Driveに同期

### 生成されるファイル

学習資料生成後、以下のファイルが作成されます：

```
YYYY-MM-DD_トピック名/
├── sources/
│   └── doc_1_xxx.md          ← 取得した資料の内容
└── output/
    ├── learning_slides.pptx  ← PowerPointスライド
    └── learning_slides.pdf   ← PDFスライド
```

### コミット例

```bash
git log --oneline
7401558 「Snowflakeクラスタリングと検索最適化」の学習資料を追加
a3b2c1d 「React Hooks実践」の学習資料を追加
```

---

## 🧠 ユーザ知識メモリ

このシステムは、ユーザの学習履歴と知識レベルを `user-memory/` ディレクトリに記録します。

### 仕組み

1. **学習前**: 過去の学習履歴を読み込み、ユーザの理解レベルを把握
2. **ユーザ診断**: 現在の知識レベルを質問で確認
3. **学習後**: 回答内容をもとに、該当ジャンルのファイルに知識を追記・更新

### ファイル構造（例: `user-memory/database.md`）

```markdown
# Database 関連の知識

## 理解している概念・技術

### SQL
- **理解レベル**: 中級（業務で使用経験あり）
- **詳細**: SELECT, JOIN, WHERE句は問題なく使える。ウィンドウ関数やCTEは名前は知っている程度
- **最終更新**: 2026-04-15

### Snowflake
- **理解レベル**: 初級（名前だけ知っている）
- **詳細**: クラスタリングキーや検索最適化の存在は知っているが、使ったことはない
- **最終更新**: 2026-04-27

## 学習済みトピック

- トランザクション分離レベル (2026-04-10)
- インデックスの基本 (2026-04-12)
- Snowflakeクラスタリングと検索最適化 (2026-04-27)
```

### メリット

- **質問の最適化**: 既に理解している概念には、より深い質問をする
- **重複の回避**: 過去に学習した内容を再度説明しない
- **学習の継続性**: 学習が進むにつれて、システムが賢くなる

---

## ☁️ Google Drive自動同期

### 仕組み

GitHubにpushされた学習資料は、**Google Apps Script (GAS)** によって定期的にGoogle Driveに同期されます。

```
GitHubリポジトリ (main)
      ↓ push
      ↓ (自動同期)
      ↓ GAS が定期実行
      ↓
Google Drive
```

### GASのセットアップ（初回のみ）

GASのセットアップ手順は、別途ドキュメントを参照してください。以下の機能を実装します：

1. **定期実行**: GASのトリガーを設定し、定期的（例: 1時間ごと）にGitHubリポジトリをチェック
2. **変更検出**: 最終同期時刻と比較し、新しいコミットがあれば取得
3. **ファイル同期**:
   - `YYYY-MM-DD_トピック名/` ディレクトリごとにGoogle Driveにアップロード
   - `user-memory/` も同期対象
   - `workspace/` は除外（.gitignoreに含まれているため）

### Google Drive上の構造

```
Google Drive: personal-learning-materials/
│
├── user-memory/
│   ├── database.md
│   ├── frontend.md
│   └── ...
│
├── 2026-04-08_snowflake-クラスタリングと検索最適化/
│   ├── sources/
│   └── output/
│       ├── learning_slides.pptx
│       └── learning_slides.pdf
│
└── 2026-04-15_react-hooks-実践/
    ├── sources/
    └── output/
        ├── learning_slides.pptx
        └── learning_slides.pdf
```

### 手動同期が不要な理由

- **自動化**: GASが定期実行されるため、手動でのアップロード操作は不要
- **一元管理**: GitHubが唯一の真実の情報源 (Single Source of Truth)
- **バックアップ**: Google Drive側は読み取り専用として扱い、編集はGitHub側で行う

---

## 🔧 トラブルシューティング

### よくある問題と解決策

#### 1. LibreOfficeが見つからない

**エラー**: `soffice: command not found`

**解決策**:
```bash
# LibreOfficeをインストール
brew install --cask libreoffice

# macOSの場合、フルパスを指定する必要がある場合があります
/Applications/LibreOffice.app/Contents/MacOS/soffice --version
```

#### 2. PDFが生成されない

**原因**: LibreOfficeが正しくインストールされていない、またはPPTXファイルが破損している

**解決策**:
1. LibreOfficeのインストールを確認
2. PPTXファイルを手動でLibreOfficeで開いて、PDF出力できるか確認
3. それでもダメな場合は、PPTXのみで進める

#### 3. git pushが失敗する

**エラー**: `Permission denied (publickey)`

**解決策**:
```bash
# SSH鍵の設定を確認
ssh -T git@github.com

# HTTPSを使用する場合
git remote set-url origin https://github.com/<USER>/<REPO>.git
```

#### 4. workspaceディレクトリがGitにコミットされる

**原因**: `.gitignore` が正しく設定されていない

**解決策**:
```bash
# .gitignore を確認
cat .gitignore

# workspace/ が含まれているか確認
# 含まれていない場合は追加
echo "workspace/" >> .gitignore

# 既にコミットされている場合は削除
git rm -r --cached workspace/
git commit -m "workspace/をGit管理から除外"
```

#### 5. user-memory/ が更新されない

**原因**: ユーザ診断の回答が記録されていない

**解決策**:
- ユーザ診断（STEP 4）で質問に回答しているか確認
- `user-memory/` ディレクトリが存在するか確認
- Claude Codeのログを確認して、エラーがないかチェック

---

## 📝 補足情報

### スライドのカスタマイズ

生成されたPPTXファイルは、PowerPointやGoogle Slidesで自由に編集できます：

- デザインテーマの変更
- スライドの追加・削除
- テキストや画像の編集

### 学習資料の再生成

同じトピックで再度学習資料を生成したい場合：

1. 既存のディレクトリ（`YYYY-MM-DD_トピック名/`）を削除またはリネーム
2. 同じURLやPDFを渡して再実行
3. 新しい日付でディレクトリが作成されます

### 複数人での利用

このシステムは個人用に設計されていますが、以下の点に注意すれば複数人でも利用可能です：

- `user-memory/` は個人の知識を記録するため、共有する場合は別々のブランチまたはリポジトリを推奨
- Google Driveの同期先を各自のアカウントに設定

---

## 🙏 謝辞

このシステムは、Claude Code CLIと以下のスキルを使用しています：

- `learning-material-generator`: 学習資料生成のメインスキル
- `pptx`: PowerPoint生成スキル
- `pdf`: PDF解析スキル

---

## 📄 ライセンス

このプロジェクトは個人利用を目的としています。

---

## 🆘 サポート

問題が発生した場合は、以下を確認してください：

1. このREADMEのトラブルシューティングセクション
2. Claude Codeの公式ドキュメント
3. GitHubのIssuesページ（存在する場合）

Happy Learning! 🎓
