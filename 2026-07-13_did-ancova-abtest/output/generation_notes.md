# 生成メモ: DID・ANCOVAデザインでのA/Bテスト

## 資料

- doc_1: 特殊なユーザーの影響を取り除く：DIDやANCOVAデザインでのA/Bテスト（note, 2026-07-12） — `sources/doc_1_did-ancova-abtest.md` 参照
- 資料は1件のみのため、統合分析（STEP3）はスキップし単体分析のみで進行。

## ユーザ診断（STEP4）の回答

- 学習目的: 業務のA/Bテスト分析に活用したい
- 現状知識: DIDはわかる・使ったことがある、基本的な検定も理解している
- 重点領域: 全部（概念理解・実装方法・使い分けの判断基準）
- 教材分量: 詳しめ（数式・応用まで深掘り）

## user-memory 更新（STEP5）

- 新規ファイル `user-memory/statistics.md` を作成し、A/Bテスト・DID・ANCOVAの理解レベルを記録。

## 学習ロードマップ（STEP6・内部メモ）

- Phase 1: ヘビーユーザー問題とノーマル分析の限界 - 目的: 問題設定の理解 - 使用資料: doc_1
- Phase 2: DID・ANCOVAの数式比較 - 目的: 2デザインの違いを回帰式レベルで理解 - 使用資料: doc_1
- Phase 3: 条件付き分散とρの理論的背景 - 目的: 元記事の主張を統計理論（Frison & Pocock 1992, CUPED）で裏付ける - 使用資料: doc_1 + 一般的な統計理論（WebSearchで確認）
- Phase 4: Python実装と実務判断手順 - 目的: 実務でそのまま使える形に落とし込む - 使用資料: doc_1 + 一般的な実装知識

理解度が高いユーザー向けに、DIDの基礎説明は簡潔にし、ANCOVAの理論的背景（元記事を超える発展知識）と実装コードに重点を置いた構成にした。理論的背景の節は元記事の主張と明確に区別して「補足」として提示し、WebSearchでFrison & Pocock (1992) とCUPED (Deng et al., 2013) の要点を確認した上で記載した。

## 静的チェック（STEP9）結果

- `docs/index.html`, `docs/styles.css`, `docs/2026-07-13_did-ancova-abtest/index.html`, `docs/2026-07-13_did-ancova-abtest/styles.css` の存在を確認済み。
- 相対リンク（一覧 -> 教材ページ、教材ページ -> styles.css）を確認済み。
- 主要セクション（表題、学べること、前提知識、ロードマップ、概念解説6件、比較表、適用判断、まとめ、参照資料）が揃っていることを確認済み。
- HTMLタグの開閉バランスをPythonスクリプトで確認済み（section/div/table/ul/ol/pre/header/main/html/body すべて一致）。
