# API仕様（Wishlist App v1）

本ドキュメントでは、Wishlist App v1 における  
**バックエンドAPI（Cloud Run）仕様**を定義する。

本APIは、ログインユーザーの Wishlist データを  
Customer メタフィールド（`wishlist.data`）へ安全に保存・更新するために使用する。


## 1. 基本方針

- APIは **ログインユーザーのみ**利用する
- ゲスト操作（localStorage）は API を使用しない
- Customer メタフィールド `wishlist.data` を単一の保存先とする
- APIは **最小構成（v1）** とし、用途を限定する

---

## 2. 認証・前提

### 2-1. 認証方式
以下いずれかの方式で「ログインユーザー本人」であることを検証する。

- App Proxy（推奨）
- Storefront API の customerAccessToken 検証

※ Admin API のアクセストークンはフロントエンドに公開しない。

---

## 3. 共通仕様

### 3-1. Base URL
https://{cloud-run-domain}/api


### 3-2. 共通ヘッダー
httpContent-Type: application/json

###3-3. 共通レスポンス（成功）
{
  "ok": true
}

###3-4. 共通レスポンス（失敗）
{
  "ok": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable message"
  }
}
## 4. お気に入り商品（variant）トグル
###4-1. 概要
variant単位でお気に入り登録 / 解除を行う。

###4-2. エンドポイント
POST /wishlist/item/toggle
###4-3. リクエスト
{
  "variant_id": 1234567890,
  "product_id": 7777777777
}
項目	必須	説明
variant_id	◯	お気に入り対象のvariant ID
product_id	△	表示補助用（取得可能な場合は必須）
###4-4. 処理内容
wishlist.data.items[] に variant_id が存在するか判定

存在しない場合：

items に追加

added_at を現在時刻（ISO8601）で設定

存在する場合：

items から削除

wishlist.data.updated_at を更新

Customer メタフィールドを保存

###4-5. レスポンス例
{
  "ok": true,
  "action": "added"
}
{
  "ok": true,
  "action": "removed"
}
##5. お気に入りコレクション（Shopify Collection）トグル
###5-1. 概要
Shopify Collection を「お気に入りコレクション」として登録 / 解除する。

###5-2. エンドポイント
POST /wishlist/collection/toggle
###5-3. リクエスト
{
  "collection_id": 1111111111,
  "handle": "training-wear"
}
項目	必須	説明
collection_id	◯	Shopify Collection の数値ID
handle	△	コレクションハンドル（取得可能な場合）
###5-4. 処理内容
wishlist.data.favorite_collections[] に collection_id が存在するか判定

存在しない場合：

favorite_collections に追加

added_at を現在時刻（ISO8601）で設定

存在する場合：

favorite_collections から削除

wishlist.data.updated_at を更新

Customer メタフィールドを保存

###5-5. レスポンス例
{
  "ok": true,
  "action": "added"
}
{
  "ok": true,
  "action": "removed"
}
##6. Wishlist データ取得
###6-1. 概要
ログインユーザーの Wishlist データを取得する。

###6-2. エンドポイント
GET /wishlist
###6-3. レスポンス
{
  "ok": true,
  "data": {
    "version": 1,
    "updated_at": "2026-01-24T10:30:00+09:00",
    "favorite_collections": [],
    "items": []
  }
}
##7. エラーハンドリング
###7-1. 認証エラー
{
  "ok": false,
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Authentication required"
  }
}
###7-2. バリデーションエラー
{
  "ok": false,
  "error": {
    "code": "INVALID_REQUEST",
    "message": "variant_id is required"
  }
}
##8. 非対応（v1）
バルク更新（複数itemsの一括操作）

お気に入り履歴の取得

共有履歴・回数の保存

管理画面向けAPI

##9. 関連ドキュメント
02_Requirements/functional_requirements.md

04_Data_Design/wishlist_schema_customer.json

04_Data_Design/wishlist_schema_guest.json

04_Data_Design/data_migration_policy.md

03_UX_UI/screen_spec_pdp.md

03_UX_UI/screen_spec_wishlist.md

