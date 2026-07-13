# doc_1: uv initしたら親のpyproject.tomlが勝手に書き換わった話

- **出典URL**: https://zenn.dev/zaspa/articles/484f0a74a416b4
- **著者**: zaspa
- **公開日**: 2026-04-15
- **プラットフォーム**: Zenn
- **取得日**: 2026-07-13

## 内容抽出（WebFetchによる要約・再構成）

### 背景・問題

- モノレポ構成の中で `dev/backend/` ディレクトリを独立したPythonプロジェクトにしようとした。
- そのディレクトリで `uv init` を実行したところ、uvがルートの `pyproject.toml`（親のpyproject.toml）を自動的に検出し、`backend` をワークスペースメンバーとして追加し、親の設定ファイルを無断で書き換えた。
- 著者が意図していたのは、Dockerで個別にコンテナ化する独立したサービスであり、依存関係を共有するワークスペース構成ではなかった。

### uvワークスペースとは

- 複数のPythonパッケージを1つの仮想環境・1つのロックファイルでまとめて管理する仕組み。
- パッケージ間に依存関係がある場合（例: backendが共通ライブラリに依存する等）には便利。

### 解決方法: `--no-workspace` フラグ

- `uv init --no-workspace` を使うことで、親ディレクトリのワークスペース自動検出を回避し、独立したプロジェクトとして初期化できる。
- 独立したプロジェクトは、それぞれ個別の `.venv` と `uv.lock` を持つ。

### 比較（デフォルト vs `--no-workspace`）

| 項目 | ワークスペース（デフォルト） | `--no-workspace` |
|------|---|---|
| ルート`pyproject.toml`への影響 | 書き換わる（`[tool.uv.workspace]`にメンバー追加） | 変更なし |
| ロックファイル | ワークスペース全体で統合管理 | プロジェクトごとに個別作成 |
| 仮想環境 | 共有 | 独立 |

### 結論（著者の主張）

- 共有コードや統一的な依存関係管理をしたい場合はワークスペース構成が向く。
- 依存関係を共有しない独立したDockerサービスとしてデプロイしたい場合は、独立プロジェクト（`--no-workspace`）の方が、CI/CDパイプラインの最適化や軽量なコンテナビルドに向く。
- ツールを初期化する前に、自分のプロジェクト構成の意図を理解しておくことが、不要なトラブルを避ける鍵になる。

## 資料分析（STEP2用メモ）

| 軸 | 内容 |
|----|------|
| 要約 | モノレポのサブディレクトリで`uv init`を実行すると、uvが親の`pyproject.toml`を自動検出しワークスペースメンバーとして書き換えてしまう問題と、`--no-workspace`フラグによる回避方法を解説する記事。 |
| 重要概念 | uvワークスペース / `[tool.uv.workspace]` / `--no-workspace`フラグ / モノレポ構成 / Docker独立デプロイ / 共有ロックファイル・共有仮想環境 |
| 前提知識 | Pythonの基本的なパッケージ管理、uvの基本コマンド（`uv init`/`uv add`/`uv run`など）の使用経験 |
| 難易度 | 中級 |
| 信頼度 | 中（個人の技術ブログ。ただし挙動は公式ドキュメント(docs.astral.sh)の記載と整合することをWebSearchで確認済み） |

## 補足で確認した公式ドキュメント情報（Astral uv docs, WebSearch/WebFetchで確認）

- `--no-workspace`: "Avoid discovering a workspace and create a standalone project. By default, uv searches for workspaces in the current directory or any parent directory."
- デフォルト挙動: "By default, running uv init inside an existing package will add the newly created member to the workspace, creating a tool.uv.workspace table in the workspace root if it doesn't already exist."
- ワークスペース設定: `[tool.uv.workspace]` は `members`（必須）と `exclude`（任意）を持つ。例: `members = ["packages/*"]`, `exclude = ["packages/seeds"]`。
- ロックファイル: "in a workspace, each package defines its own pyproject.toml, but the workspace shares a single lockfile, ensuring that the workspace operates with a consistent set of dependencies."
- 設定ファイル・仮想環境の探索: "All pyproject.toml, uv.toml, and .python-version files will be discovered by walking up the directory tree from the project root, as will the project's virtual environment (.venv)."
- 出典: https://docs.astral.sh/uv/concepts/projects/workspaces/ , https://docs.astral.sh/uv/reference/cli/
