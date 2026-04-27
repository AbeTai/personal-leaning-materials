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
- PPTXを必ず生成、PDFは環境により生成（デスクトップ版のみ）

### 4. **Git管理 + Google Drive同期**
- 成果物を自動的にGitHubリポジトリにコミット・プッシュ
- Google Apps Script (GAS) が定期的にGitHubを監視し、**PDFのみ**をGoogle Driveに自動同期

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

#### **Claude Code環境**

このシステムは以下の環境で動作します：

| 環境 | 説明 | PDF生成 |
|------|------|---------|
| **デスクトップ版Claude Code** | ローカルマシンで実行 | ✅ 可能（LibreOfficeインストール可） |
| **スマホ版Claude Code** | Sandbox環境で実行 | ❌ 不可（PPTXのみ生成） |

#### **必須ツール**

1. **Claude Code**
   - デスクトップ版: [公式サイト](https://claude.ai/code)からCLIをインストール
   - スマホ版: ClaudeアプリからClaude Code機能を使用

2. **Git & GitHub**
   - GitHubアカウントを作成
   - このリポジトリをforkまたは自分のリポジトリを作成

#### **オプショナルツール（デスクトップ版のみ）**

3. **LibreOffice**（PPTX → PDF変換用）
   ```bash
   # macOS
   brew install --cask libreoffice

   # Linux (Ubuntu/Debian)
   sudo apt-get install libreoffice
   ```

   **注意**: スマホ版Claude Code（sandbox環境）ではLibreOfficeをインストールできないため、PPTXのみ生成されます。PDFが必要な場合は、PPTXを手動でPDF変換してください。

### 初回セットアップ手順

#### **方法A: このリポジトリをテンプレートとして使う（推奨）**

第三者がこのシステムを使う場合の手順です：

```bash
# 1. このリポジトリをfork（GitHubのWebUI上で実行）
# または、Use this template ボタンをクリック

# 2. 自分のGitHubリポジトリをclone
git clone https://github.com/<YOUR_USERNAME>/personal-leaning-materials.git
cd personal-leaning-materials

# 3. リモートリポジトリの設定を確認
git remote -v
```

#### **方法B: 新規にリポジトリを作成する**

ゼロから始める場合の手順です：

```bash
# 1. 新しいディレクトリを作成
mkdir personal-leaning-materials
cd personal-leaning-materials

# 2. Gitリポジトリを初期化
git init

# 3. GitHubで空のリポジトリを作成してから、リモートを追加
git remote add origin https://github.com/<YOUR_USERNAME>/personal-leaning-materials.git
```

#### 2. 必要なディレクトリの作成（方法Bの場合のみ）

**方法A（テンプレート使用）の場合はスキップ**してください。既にディレクトリが存在します。

```bash
mkdir -p user-memory
touch user-memory/.gitkeep
```

#### 3. .gitignore の設定（方法Bの場合のみ）

**方法A（テンプレート使用）の場合はスキップ**してください。既に `.gitignore` が存在します。

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

#### 4. Claude Codeへの接続

##### **デスクトップ版Claude Code (CLI)の場合**

```bash
# プロジェクトディレクトリで Claude Code を起動
cd personal-leaning-materials
claude

# skillファイルは自動的に認識されます
```

##### **スマホ版Claude Codeの場合**

1. Claudeアプリを開く
2. Claude Code機能を選択
3. GitHubリポジトリに接続：
   - `Connect to GitHub repository` を選択
   - リポジトリURL: `https://github.com/<YOUR_USERNAME>/personal-leaning-materials`
   - 接続を承認

4. skillファイルは自動的に認識されます
   - プロジェクトルートの `learning-material-generator.skill` が自動で読み込まれる

#### 5. 初回コミット（方法Bの場合のみ）

**方法A（テンプレート使用）の場合はスキップ**してください。

```bash
git add .
git commit -m "初回コミット: 学習資料生成システムのセットアップ"
git push -u origin main
```

---

## 💡 使い方

### 基本的な使い方

#### **デスクトップ版Claude Code (CLI)**

プロジェクトディレクトリでClaude Codeを起動し、以下のように資料を渡します：

```bash
# Claude Codeを起動
cd personal-leaning-materials
claude
```

チャットで以下のように指示：

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

#### **スマホ版Claude Code**

1. Claudeアプリを開き、Claude Code機能を起動
2. GitHubリポジトリ（`personal-leaning-materials`）に接続済みであることを確認
3. チャットで以下のように指示：

```
この資料からスライドを作って：
https://example.com/article1
```

**注意**: スマホ版ではPDFは生成されません（LibreOfficeが利用不可のため）。PPTXのみ生成されます。

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

GitHubにpushされた学習資料の**PDFとPPTXファイル**を、**Google Apps Script (GAS)** が定期的にGoogle Driveに同期します。

```
GitHubリポジトリ (main)
      ↓ push
      ↓ (自動同期)
      ↓ GAS が定期実行
      ↓ PDF & PPTX 抽出
      ↓
Google Drive
```

### 同期対象

| ファイル | 同期 | 理由 |
|---------|------|------|
| `output/learning_slides.pdf` | ✅ する | 閲覧・共有用の最終成果物 |
| `output/learning_slides.pptx` | ✅ する | 編集可能な元ファイル |
| `sources/` | ❌ しない | 元資料はGitHubで管理 |
| `user-memory/` | ❌ しない | 個人の学習履歴はGitHubで管理 |

### GASのセットアップ（初回のみ）

GASのセットアップ手順は、別途ドキュメントを参照してください。以下の機能を実装します：

1. **定期実行**: GASのトリガーを設定し、定期的（例: 1時間ごと）にGitHubリポジトリをチェック
2. **変更検出**: 最終同期時刻と比較し、新しいコミットがあれば取得
3. **ファイル抽出**:
   - `YYYY-MM-DD_トピック名/output/learning_slides.pdf` を対象
   - `YYYY-MM-DD_トピック名/output/learning_slides.pptx` を対象
   - トピック名ごとにフォルダを作成してアップロード
   - 既存ファイルは上書き

### Google Drive上の構造

```
Google Drive: personal-learning-materials/
│
├── 2026-04-08_snowflake-クラスタリングと検索最適化/
│   ├── learning_slides.pdf
│   └── learning_slides.pptx
│
├── 2026-04-15_react-hooks-実践/
│   ├── learning_slides.pdf
│   └── learning_slides.pptx
│
└── 2026-04-20_aws-lambda-入門/
    ├── learning_slides.pdf
    └── learning_slides.pptx
```

**トピックごとのフォルダ構造**: 各学習資料ごとにフォルダを作成し、PDFとPPTXをセットで管理します。

### 手動同期が不要な理由

- **自動化**: GASが定期実行されるため、手動でのアップロード操作は不要
- **一元管理**: GitHubが唯一の真実の情報源 (Single Source of Truth)
- **編集可能**: PPTXをGoogle Driveで直接編集したい場合にも対応

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
