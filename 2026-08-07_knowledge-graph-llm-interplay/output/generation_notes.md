# 生成メモ: ナレッジグラフとLLMの相互利用

## 資料

- doc_1: ナレッジグラフとLLMの相互利用（古崎晃司, 大阪電気通信大学, 2024-09-12） — `sources/doc_1_knowledge-graph-llm-interplay.md` 参照
- 資料は1件のみのため、統合分析（STEP3）はスキップし単体分析のみで進行。
- スライドで具体的な実装詳細が述べられていなかったMicrosoft GraphRAGの仕組み（Leiden法によるコミュニティ検出、ローカル/グローバル検索）はWebSearchで補足し、公式リポジトリ・原論文（arXiv 2404.16130）を出典として明記した。

## STEP0/STEP4（継続性とユーザ診断）について

- `user-memory/data-engineering.md` を確認したところ、2026-07-21の教材でグラフDB・ナレッジグラフ・オントロジーの基礎（ノード・エッジ、活用事例）を学習済みであることが分かった。
- 直近2回の教材でAskUserQuestionによる診断がユーザーに拒否された経緯を踏まえ、本教材でも診断を省略し、前回教材からの継続を前提に、RDF/SPARQLという具体的な記法とLLMとの組み合わせ（特にGraph RAG）に重点を置いた詳しめの構成とした。

## user-memory 更新（STEP5相当）

- `user-memory/data-engineering.md` に「ナレッジグラフとLLMの相互利用（RDF/SPARQL/Graph RAG）」の項目を追記し、学習済みトピックと学習目的も更新した。

## 学習ロードマップ（STEP6・内部メモ）

- Phase 1: RDF・Linked Data・SPARQLの基礎 - 使用資料: doc_1
- Phase 2: オントロジーの役割とKGQAの仕組み - 使用資料: doc_1
- Phase 3: KGとLLMの6つの相互利用形態 - 使用資料: doc_1
- Phase 4: Graph RAGの技術的な仕組み（Microsoft GraphRAG） - 使用資料: doc_1 + WebSearchでの補足（Leiden法、コミュニティ検出）

前回教材（セマンティック概論）への相互リンクを設置し、シリーズとしての継続性を明示した。

## 静的チェック（STEP9）結果

- `docs/index.html`, `docs/styles.css`, `docs/2026-08-07_knowledge-graph-llm-interplay/index.html`, `docs/2026-08-07_knowledge-graph-llm-interplay/styles.css` の存在を確認済み。
- 一覧ページおよび教材ページ内の相対リンク（一覧へ、前回教材へ）が実在するディレクトリを指していることを確認済み。
- HTMLタグの開閉バランスをPythonスクリプトで確認済み（section/div/table/ul/ol/pre/header/main/html/body すべて一致）。
- Markdown由来の不要なバッククォートが残っていないことを確認済み。
