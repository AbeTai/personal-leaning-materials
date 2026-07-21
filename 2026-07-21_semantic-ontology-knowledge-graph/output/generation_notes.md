# 生成メモ: セマンティック概論（オントロジー・グラフDB・ナレッジグラフ）

## 資料

- doc_1: セマンティック概論 - #GoogleCloudNext '26 Recap（横山翔, 株式会社風音屋, 2026-06-04） — `sources/doc_1_semantic-ontology-knowledge-graph.md` 参照
- 資料は1件のみのため、統合分析（STEP3）はスキップし単体分析のみで進行。
- スライドで言及されていたBigQuery Graph / BigQuery Measures（Google Cloud Next '26発表）はWebSearchで実在を確認し裏付けを取った。BigQuery GraphのGQL構文は公式ドキュメントに具体例が見つからなかったため、教材内では「イメージ」として明記し厳密な構文を断定しない形にした。

## 前回教材からの継続性（STEP0）

- `user-memory/data-engineering.md` を確認したところ、2026-07-14に「データレイク/DWH/マートの三層構造」「メタデータ管理（初級・重点領域として学習）」を学習済みであることが分かった。
- 本教材はその続編として位置づけ、三層構造・メタデータの基礎説明は簡潔にし、新規概念（セマンティックレイヤーの深掘り、統制語彙、オントロジー、グラフDB、ナレッジグラフ）に重点を置いた。

## ユーザ診断（STEP4）の回答

- 学習目的: 自分のデータ基盤・メタデータ整備の次のステップとして知りたい
- 現状知識: dbtやLookerのセマンティックレイヤー機能は使ったことがある
- 重点領域: グラフDB・ナレッジグラフの考え方と活用事例
- 教材分量: 詳しめ（事例・AIエージェント応用まで深掘り）

## user-memory 更新（STEP5）

- `user-memory/data-engineering.md` に「セマンティックレイヤー（dbt/Looker）」「オントロジー・統制語彙」「グラフDB・ナレッジグラフ」の3項目を追記し、学習済みトピックと学習目的も更新した。

## 学習ロードマップ（STEP6・内部メモ）

- Phase 1: なぜ今セマンティックが重要か - 目的: AIエージェントが誤る具体例からの導入 - 使用資料: doc_1
- Phase 2: 統制語彙とセマンティックレイヤーの整理 - 目的: 既知の概念（セマンティックレイヤー）の再確認と新規概念（統制語彙）の整理 - 使用資料: doc_1
- Phase 3: グラフDB・ナレッジグラフ（重点領域） - 目的: ノード・エッジ構造、隣接行列、活用事例を深掘りする - 使用資料: doc_1 + Google Cloud公式ブログ（BigQuery Graph裏付け）
- Phase 4: AIエージェント・RAGへの応用 - 目的: コンテキストレイヤーという考え方への接続 - 使用資料: doc_1

「詳しめ・事例まで深掘り」の指定に応じて、重点領域であるグラフDB・ナレッジグラフの節を2つのConceptセクション（グラフDBの基本+隣接行列の具体例、ナレッジグラフの活用事例）に分けて厚めに構成した。

## 静的チェック（STEP9）結果

- `docs/index.html`, `docs/styles.css`, `docs/2026-07-21_semantic-ontology-knowledge-graph/index.html`, `docs/2026-07-21_semantic-ontology-knowledge-graph/styles.css` の存在を確認済み。
- 一覧ページから教材ページへの相対リンクを確認済み。
- HTMLタグの開閉バランスをPythonスクリプトで確認済み（section/div/table/ul/ol/pre/header/main/html/body すべて一致）。
- Markdown由来の不要なバッククォートが残っていないことを確認済み。
