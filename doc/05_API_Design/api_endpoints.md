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
本APIにリクエストする際は、以下の HTTP ヘッダーを必須とする。

```http
Content-Type: application/json
```
補足
- 本APIは JSON形式のリクエストボディのみ を受け付ける
- POST リクエストでは 必須
- GET リクエストでは 付与してもよい（推奨）

```json
### 3-3. 共通レスポンス（成功）
{
  "ok": true
}
```
### 3-4. 共通レスポンス（失敗）
すべてのAPIは失敗時、以下の形式でレスポンスを返す。
```json
{
  "ok": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable message"
  }
}
```
♯♯♯　エラーコード例

| code              | 説明            |
| ----------------- | ------------- |
| `UNAUTHORIZED`    | 認証が必要 / 認証に失敗 |
| `INVALID_REQUEST` | パラメータ不正       |
| `NOT_FOUND`       | 対象が存在しない      |
| `INTERNAL_ERROR`  | サーバー内部エラー     |


---

## 4. お気に入り商品（variant）トグル
### 4-1. 概要
variant単位でお気に入り登録 / 解除を行う。

### 4-2. エンドポイント
POST /wishlist/item/toggle

### 4-3. リクエスト
```http
{
  "variant_id": 1234567890,
  "product_id": 7777777777
}
```
| 項目         | 必須 | 説明                  |
| ---------- | -- | ------------------- |
| variant_id | ◯  | お気に入り対象の Variant ID |
| product_id | △  | 表示補助用（取得可能な場合）      |


### 4-4. 処理内容
1.wishlist.data.items[] に variant_id が存在するか判定

存在しない場合：
- items に追加
- added_at を現在時刻（ISO8601）で設定

存在する場合：
- items から削除
2.wishlist.data.updated_at を更新
3.Customer メタフィールドを保存

### 4-5. レスポンス例
```json
{
  "ok": true,
  "action": "added"
}
{
  "ok": true,
  "action": "removed"
}
```
```json
{
  "ok": true,
  "action": "removed"
}
```
## 5. Wishlist データ取得
### 5-1. 概要
ログインユーザーの Wishlist データを取得する。

### 5-2. エンドポイント
```http
GET /wishlist
```

### 5-3. レスポンス
```http
{
  "ok": true,
  "data": {
    "version": 1,
    "updated_at": "2026-01-24T10:30:00+09:00",
    "items": []
  }
}
```

---

## 6. エラーハンドリング
### 6-1. 認証エラー
```json
{
  "ok": false,
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Authentication required"
  }
}
```
### 6-2. バリデーションエラー
```json
{
  "ok": false,
  "error": {
    "code": "INVALID_REQUEST",
    "message": "variant_id is required"
  }
}
```
---

## 7. 非対応（v1）
- バルク更新（複数itemsの一括操作）
- お気に入り履歴の取得
- 共有履歴・回数の保存
- 管理画面向けAPI

---

## 8. 関連ドキュメント
- 02_Requirements/functional_requirements.md
- 04_Data_Design/wishlist_schema_customer.json
- 04_Data_Design/wishlist_schema_guest.json
- 04_Data_Design/data_migration_policy.md
- 03_UX_UI/screen_spec_pdp.md
- 03_UX_UI/screen_spec_wishlist.md
