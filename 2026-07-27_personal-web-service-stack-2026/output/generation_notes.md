# 生成メモ: 個人的Webサービスを作るときの構成 2026

## 資料

- doc_1: 個人的Webサービスを作るときの構成 2026（catnose, X） — `sources/doc_1_personal-web-service-stack-2026.md` 参照
- 資料は1件のみのため、統合分析（STEP3）はスキップし単体分析のみで進行。
- XのURLはWebFetchで直接取得できなかった（402 Payment Required。fxtwitter/vxtwitter/nitter経由でも取得不可）ため、ユーザーが投稿本文を直接会話に貼り付けたものを一次資料として採用した。
- 投稿内で言及されていた各サービスの現状（Cloudflare Email Serviceのパブリックベータ化、PlanetScaleのPostgres GA、OpenNext/TanStack StartのCloudflare Workers対応、NeonのTokyo未対応、SendGrid無料プラン終了）はWebSearchで裏付けを取った。

## STEP4（ユーザ診断）について

- 直前の教材（データ基盤アーキテクチャ）でAskUserQuestionによる診断がユーザーに拒否された経緯があったため、本教材でも診断を省略し、一般的な個人開発者を想定した標準〜やや詳しめの深さで構成した。

## user-memory 更新（STEP5相当）

- 新規ファイル `user-memory/infrastructure.md` を作成し、個人Webサービスの技術構成に関する学習内容を記録した。

## 学習ロードマップ（STEP6・内部メモ）

- Phase 1: 静的サイトの選び方 - 使用資料: doc_1
- Phase 2: サーバーが必要な場合の選び方 - 使用資料: doc_1
- Phase 3: DB・ストレージの選び方 - 使用資料: doc_1 + WebSearchでの裏付け
- Phase 4: メール配信・認証・決済・アクセス制御 - 使用資料: doc_1 + WebSearchでの裏付け（SendGrid終了、Cloudflare Email Service）
- Phase 5: 複雑な要件でのGoogle Cloud活用とAIへの相談という締め - 使用資料: doc_1

元投稿の構造（▼で区切られたカテゴリ）をそのままConceptセクションの単位として採用し、各カテゴリを「要件・選択肢・選ぶ理由」の表で整理した上で、具体例・判断の軸・理解チェックを追加する構成にした。

## 静的チェック（STEP9）結果

- `docs/index.html`, `docs/styles.css`, `docs/2026-07-27_personal-web-service-stack-2026/index.html`, `docs/2026-07-27_personal-web-service-stack-2026/styles.css` の存在を確認済み。
- 一覧ページから教材ページへの相対リンクを確認済み。
- HTMLタグの開閉バランスをPythonスクリプトで確認済み（section/div/table/ul/ol/pre/header/main/html/body すべて一致）。
- Markdown由来の不要なバッククォートが残っていないことを確認済み。
