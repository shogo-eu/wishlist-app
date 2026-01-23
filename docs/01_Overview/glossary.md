# 用語集（Glossary）

本ドキュメントでは、Wishlist App に関する用語・略語・概念を定義する。  
認識ズレ防止のため、実装・仕様議論では本定義を正とする。

---

## Wishlist（ウィッシュリスト / お気に入り）
ユーザーが「後で見返したい」「検討中」の商品（variant）を保存する機能。

本アプリでは **variant単位**で保存される。

---

## Favorite Item（お気に入りアイテム）
Wishlistに登録された **1つのvariant** を指す。

- 主キー：`variant_id`
- 補助情報：`product_id`
- ゲスト：localStorageに保存
- ログイン：Customer metafieldに保存

---

## Variant（バリアント）
Shopifyにおける商品バリエーション。

例：
- サイズ（S / M / L）
- カラー（Red / Blue）

本アプリでは **Wishlistの最小単位**。

---

## Product（プロダクト）
Shopifyにおける商品本体。

Wishlistでは直接の保存単位にはせず、  
表示・復元・リンク用途として `product_id` を併記する。

---

## Guest User（ゲストユーザー）
Shopifyにログインしていないユーザー。

- お気に入り登録・解除：可能
- コレクション（整理機能）：不可
- 保存先：ブラウザの localStorage

---

## Logged-in User（ログインユーザー）
Shopifyにログインしているユーザー（Customer）。

- お気に入り登録・解除：可能
- コレクション（整理機能）：可能
- 保存先：Customer metafield

---

## Wishlist Collection（お気に入りコレクション）
ログインユーザーが作成できる、  
お気に入りを整理するための **ユーザー定義フォルダ/リスト**。

- Shopifyの「Collection」とは **別物**
- 1アイテムを **複数コレクションに所属可能**
- v1ではログインユーザーのみ利用可能

---

## Uncategorized（未分類）
お気に入りコレクションに属していないアイテムの状態。

- 新規追加時のデフォルト状態
- コレクション削除時の退避先

---

## localStorage
ブラウザに保存されるクライアントサイドストレージ。

- ゲストユーザーのWishlist保存に使用
- JSON形式で保持
- ブラウザ/端末単位（アカウント非共有）

---

## Customer Metafield
ShopifyのCustomerに紐づくカスタムデータ領域。

- ログインユーザーのWishlistデータを保存
- items / collections / 分類情報をJSONで管理
- Shopify Admin画面から内容確認は可能（編集は非推奨）

---

## Cloud Run
Google Cloudのサーバーレス実行環境。

本アプリでは以下の役割を担う：
- Wishlist APIの提供
- Customer metafield の更新
- 認証検証（本人確認）

---

## Admin API（Shopify）
Shopifyが提供する管理者向けAPI。

- Customer metafieldの読み書きに使用
- フロントエンドから直接呼ばない（必ずサーバー経由）

---

## Storefront API
Shopifyが提供するフロントエンド向けAPI。

- 認証済み customerAccessToken の検証に使用する可能性あり
- 商品情報取得用途としても利用可能

---

## App Proxy
Shopifyアプリの仕組みの一つ。

- Shopify経由でAPIリクエストを中継
- リクエスト署名により「本人性」を担保できる
- Wishlist APIの認証方式候補の一つ

---

## Theme App Extension
Shopifyテーマにブロックとして機能を提供する仕組み。

- v1では **使用しない**
- 将来的な配布・再利用フェーズで検討対象

---

## In Scope / Out of Scope
- **In Scope**：v1で実装対象とする機能
- **Out of Scope**：v1では実装しない（将来検討）

仕様の膨張・手戻りを防ぐため、常に明示する。

---

## v1
本ドキュメント群で定義する **初期リリース版**。

- 最速実装
- 保守性重視
- 拡張余地を残す

を前提とする。
