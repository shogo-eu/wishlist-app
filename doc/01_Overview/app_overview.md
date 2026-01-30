# Wishlist App 概要（variant + collection）

## 1. 目的（Why）
Shopifyストアにおける購入検討を支援するため、Wishlist（お気に入り）機能を提供する。  
標準機能では不足する「後で見返す」「比較する」「整理する」体験を補完し、CVR向上・回遊促進に寄与する。

---

## 2. 解決したい課題
- サイズ/カラー（variant）ごとに検討したいが、商品（product）単位のお気に入りでは意図がズレる
- 今すぐ購入しない商品を後で探すのが手間（再訪時に見つけにくい）
- 検討中の商品を目的別に整理したい（例：部活用/ギフト/追加購入候補）

---

## 3. 基本方針（Design Principles）

### 3-1. お気に入りは variant 単位で保存する
- 保存単位は **variant_id** を主とし、表示・復元用に **product_id** を併記する
- variant切替に追従して「保存済み/未保存」を正しく表現する

### 3-2. ゲストとログインの両方で“お気に入り保存”を提供する
- ゲストでもお気に入り登録は可能とし、体験の入口を広げる
- ゲスト保存はブラウザ（localStorage）に保存し、ログイン後に引き継げる設計とする

### 3-3. 整理（コレクション）はログインの価値として提供する
- ゲストは「All（一覧）」のみ閲覧・管理できる
- ログイン誘導は自然な価値提示として行う（強制しない）

### 3-4. v1は最速実装・保守容易性を優先する
- Theme App Extension は v1 では使用しない（テーマへ直接組み込み）
- 管理画面（Shopify Admin）向けUIは提供しない（データはmetafieldで確認可能）

---

## 4. ユーザー区分と提供機能

| 区分 | お気に入り（variant） | コレクション（整理） | 保存先 |
|---|---|---|---|
| ゲスト | ✅ 追加/削除 | ❌ 利用不可 | localStorage |
| ログイン | ✅ 追加/削除 | ✅ 作成/編集/分類 | Customer metafield |

---

## 5. 機能スコープ（v1）

### 5-1. 含める（In Scope）
- お気に入り登録/解除（variant単位）
- ゲスト保存（localStorage）
- ログイン保存（Customer metafield）
- ログイン時のゲスト→ログイン引き継ぎ（和集合マージ）
- ログイン時のコレクション機能
  - 作成 / リネーム / 削除
  - お気に入りアイテムの分類（複数コレクション付与を許容）

### 5-2. 含めない（Out of Scope / v1ではやらない）
- Theme App Extension 化（ブロック提供）
- 複数ストア配布（汎用SaaS化）
- Shopify Admin内のアプリUI（設定画面/ログビュー等）
- レコメンド機能、通知（メール/プッシュ）、行動分析
- 未ログイン状態でのコレクション利用（作成/分類）

---

## 6. 技術前提（High-level）

### 6-1. フロント（Shopifyテーマ）
- Liquid + JavaScript
- PDPでvariant切替に追従したお気に入り状態表示
- お気に入りページ（/pages/wishlist 等）をテーマ側で用意
- ゲストはlocalStorageを参照して表示
- ログインはサーバー（API）から取得/更新する

### 6-2. バックエンド（API）
- Cloud Run 上にAPIを実装
- Shopify Admin APIで Customer metafield を更新
- 認証は以下のいずれかを採用（要検討・別ドキュメントに詳細）
  - App Proxy
  - Storefront customerAccessToken を用いた検証

### 6-3. データ保存
- ゲスト：localStorage（itemsのみ）
- ログイン：Customer metafield（items + collections + itemの分類）

---

## 7. 成功条件（Definition of Done / v1）
- ゲストでも直感的にお気に入り登録/解除ができる（variant単位のブレがない）
- ログイン後にゲストのお気に入りが自然に引き継がれる
- ログインユーザーはコレクションで整理できる
- テーマ実装とAPI実装が疎結合で、保守・拡張しやすい

---

## 8. 次に参照するドキュメント
- `02_Requirements/functional_requirements.md`（機能要件）
- `03_UX_UI/screen_spec_*.md`（画面仕様）
- `04_Data_Design/`（JSONスキーマ / metafield定義）
- `05_API_Design/api_endpoints.md`（API仕様）
- `06_Auth_Security/auth_strategy.md`（認証戦略）
