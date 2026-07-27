# doc_1: 個人的Webサービスを作るときの構成 2026

- **出典URL**: https://x.com/catnose99/status/2079777959167340607?s=46
- **投稿者**: catnose（@catnose99）。Zenn創業者・個人開発者。sizu.meなどの個人開発サービスを運営
- **プラットフォーム**: X（旧Twitter）
- **取得日**: 2026-07-27
- **取得方法**: XはWebFetchで直接取得できなかった（402 Payment Required）ため、ユーザーが投稿本文を直接貼り付けたものを資料として採用

## 内容（ユーザー提供の投稿本文全文）

```
個人的Webサービスを作るときの構成 2026

▼ 1ページだけの簡単なWebページ
→ htmlファイル書かせてCloudflareに置く

▼ 静的ビルドできるやつ
・単純なやつ → React + ViteでCloudflareに置く
・ちょい複雑 → Next.jsでStatic ExportしてCloudflareに置く
・記事っぽいコンテンツ中心 → AstroでCloudflareに置く
・ちょっとしたAPIが必要 → Workersにエンドポイントを生やすかHono使う

▼ サーバー必要
・小規模〜中規模 → TanStack Start on CF Workers
・中〜大規模。最短で出したい → Next.js on Vercel
・中規模でコスト削減したい。苦しむ覚悟がある → OpenNext on CF Workers

▼ DB
・小〜中規模 & CF Workersを使う → D1 / Durable Objects
・Cloudflare以外で小〜中規模 → Turso
・Postgres使いたいが安くしたい → 国内向けならSupabase、じゃなければNeon（東京リージョンないので）
・Postgres or MySQL使いたい & 多少コストかかっていい → PlanetScale

▼ ストレージ
・節約したい → R2
・吹っ飛んだら詰む → AWS S3 or GCS（バージョニングを有効にする）
・節約したい & 吹っ飛んだら詰む → R2 + 定期バックアップ（S3 or GCSの最安ストレージクラス）

▼ メール配信
・たくさん送る → AWS SES（導入めんどうだけど安い）
・Workers使ってる → Cloudflare Email Service 🆕
・あんまり送らない → Resend
（SendGridは無料プランがなくなったし候補外）

▼ その他
・決済 → Stripe
・認証 → BetterAuthを検討して要件合わなかったら何かサービス使う
・リアルタイム同期したい → Cloudflare WorkersのDurable Objects使う
・一部の人に向けて公開したい → Cloudflare Zero Trust

▼ ビジネス的な要件が色々ある
→ Google Cloudのサービスを組み合わせる（Cloud Run / SQL / Memorystore / Tasks / Scheduler / Storage）

とかいいつつ、結局サービスの内容によって変わるのでAIに相談して決めてもらうのが良い
```

## 資料分析（STEP2用メモ）

| 軸 | 内容 |
|----|------|
| 要約 | 個人開発者が2026年時点でWebサービスを作る際の技術構成を、要件（静的か動的か、規模、コスト、コンテンツ特性）ごとに整理した実践的な判断フローチャート。フロントエンド/サーバー/DB/ストレージ/メール配信/認証・決済/複雑なビジネス要件まで一通りカバーしている。 |
| 重要概念 | 静的サイト生成（React+Vite/Next.js Static Export/Astro）/ Cloudflare Workers・Hono / TanStack Start / OpenNext / D1・Durable Objects / Turso / Supabase・Neon・PlanetScale / R2・S3・GCS / Cloudflare Email Service・AWS SES・Resend / Stripe・BetterAuth・Cloudflare Zero Trust / Google Cloudの各種マネージドサービス |
| 前提知識 | Webアプリケーション開発の基礎（フロントエンド/バックエンド/DB/ストレージの役割）、主要クラウドサービス（AWS/GCP/Cloudflare）の存在を知っている程度 |
| 難易度 | 中級（個々の技術要素の詳細説明はなく、選定の判断軸が中心。技術要素ごとの基礎知識があるとより深く理解できる） |
| 信頼度 | 中（個人開発者による経験則ベースの整理。査読はないが、著者はZenn創業者で個人開発の実績が豊富） |

## 補足で確認した情報（WebSearchで確認、2026年7月時点）

- **Cloudflare Email Service**: 2026年4月16日にEmail Sending機能がパブリックベータ入り。Workersからネイティブバインディング（`env.EMAIL.send()`）でメール送信でき、SPF/DKIM/DMARCが自動設定される。Workers Paidプランで利用可能。
- **PlanetScale for Postgres**: 2025年にプライベートプレビューからGA（一般提供）へ。MySQLに加えPostgresもサポート。
- **OpenNext (Cloudflare adapter)**: `@opennextjs/cloudflare`アダプタでNext.jsのビルド出力をCloudflare Workersで動作する形式に変換してデプロイ可能。Node.js互換フラグ（`nodejs_compat`）の有効化が必要。
- **TanStack Start on Cloudflare Workers**: CloudflareはTanStackの公式パートナーで、Nitroのプリセットを`cloudflare-module`に設定し、`wrangler deploy`で自動検出・デプロイが可能。
- **Neonのリージョン**: 2026年7月時点でTokyo（東京）リージョンは提供されておらず、アジア太平洋はSingaporeのみ。投稿内の「Neon（東京リージョンないので）」という記載と一致。
- **SendGrid無料プラン終了**: Twilio SendGridは2025年5月27日から無料プランの提供終了を開始し、2025年7月26日に完全終了。新規アカウントは60日間のトライアル後、有料プラン（月額19.95ドル〜）のみとなった。投稿内の「SendGridは無料プランがなくなった」という記載と一致。
- 出典: https://developers.cloudflare.com/changelog/post/2026-04-16-email-sending-public-beta/ , https://planetscale.com/blog/planetscale-for-postgres-is-generally-available , https://opennext.js.org/cloudflare , https://developers.cloudflare.com/workers/framework-guides/web-apps/tanstack-start/ , https://neon.com/docs/introduction/regions , https://www.twilio.com/en-us/changelog/sendgrid-free-plan
