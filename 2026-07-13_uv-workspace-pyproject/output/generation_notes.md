# 生成メモ: uv initしたら親のpyproject.tomlが勝手に書き換わった話

## 資料

- doc_1: uv initしたら親のpyproject.tomlが勝手に書き換わった話（zaspa, Zenn, 2026-04-15） — `sources/doc_1_uv-workspace-pyproject.md` 参照
- 資料は1件のみのため、統合分析（STEP3）はスキップし単体分析のみで進行。
- 記事本文の技術的な挙動（`--no-workspace`の効果、ワークスペース検出ロジック、ロックファイル・仮想環境の共有）はuv公式ドキュメント（docs.astral.sh）をWebSearch/WebFetchで確認し、裏付けを取った上で教材に反映した。

## ユーザ診断（STEP4）の回答

- 学習目的: uvの仕組みを教養として理解しておきたい
- 現状知識: 基本的なコマンド（uv init/add/run）は使ったことがある
- 重点領域: 実務での対処法・回避策
- 教材分量: 詳しめ（挙動の検証・応用まで深掘り）

## user-memory 更新（STEP5）

- 新規ファイル `user-memory/devops.md` を作成し、uvの理解レベルと学習履歴を記録。

## 学習ロードマップ（STEP6・内部メモ）

- Phase 1: 何が起きたかの再現 - 目的: 事象そのものの理解 - 使用資料: doc_1
- Phase 2: ワークスペース自動検出の仕組み - 目的: 公式ドキュメントで検出ロジックを裏付ける - 使用資料: doc_1 + uv公式docs
- Phase 3: 実務での回避策・対処法 - 目的: --no-workspaceと既存メンバーの分離手順を身につける（重点領域） - 使用資料: doc_1 + uv公式docs
- Phase 4: モノレポ設計への応用 - 目的: ワークスペースを使うべきか独立プロジェクトにすべきかの判断基準 - 使用資料: doc_1

現状知識が「基本コマンドは使ったことがある」レベルであるため、uvの基本操作説明は省略し、ワークスペース検出の仕組みと実務対処法（重点領域として回答）に多くの分量を割いた。

## 静的チェック（STEP9）結果

- `docs/index.html`, `docs/styles.css`, `docs/2026-07-13_uv-workspace-pyproject/index.html`, `docs/2026-07-13_uv-workspace-pyproject/styles.css` の存在を確認済み。
- 一覧ページから教材ページへの相対リンクを確認済み。
- 教材ページ内で使用していたMarkdown形式のバッククォート（`` `word` ``）を`<code>`タグに置換する修正を実施済み（静的HTMLとして正しくレンダリングされるようにするため）。
- HTMLタグの開閉バランスをPythonスクリプトで確認済み（section/div/table/ul/ol/pre/header/main/html/body すべて一致）。
