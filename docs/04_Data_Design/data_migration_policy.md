# ゲスト→ログイン引き継ぎ（Migration Policy）

本ドキュメントでは、ゲスト保存（localStorage）のWishlistを  
ログイン後に Customer metafield `wishlist.data` へ引き継ぐルールを定義する。

※ 本仕様は v1（favorite_collectionsあり／アイテム分類なし）を前提とする。

---

## 1. 対象データ

### 1-1. ゲスト側（localStorage）
- スキーマ：`wishlist_schema_guest.json`
- 対象：`items[]` のみ（favorite_collections は存在しない）

### 1-2. ログイン側（Customer metafield）
- 保存先：`wishlist.data`
- スキーマ：`wishlist_schema_customer.json`
- 対象：
  - `items[]`
  - `favorite_collections[]`

---

## 2. 実行タイミング

以下いずれかの「ログイン後初回アクセス」で 1 回実行する。
- Wishlistページ（例：`/pages/wishlist`）の初回表示時
- Account / My page の初回表示時

---

## 3. 引き継ぎの前提条件

- ログイン状態（Customerが確定）であること
- ゲスト側 localStorage に `items[]` が1件以上存在すること
- ログイン側 `wishlist.data` が未設定の場合は、空データとして扱う

---

## 4. マージルール（最重要）

### 4-1. items（variantお気に入り）のマージ
- マージ単位：`variant_id`
- ルール：**和集合（重複排除）**
  - `variant_id` が同一のものは 1 件に統合する
- 統合時の優先順位（v1推奨）
  - `added_at` は **既存（ログイン側）を優先**  
    （ログイン側に無ければゲスト側を採用）
  - `product_id` は **値がある方を採用**（片方がnull/欠損なら補完）

### 4-2. favorite_collections の扱い
- ゲスト側には存在しないため、**マージ対象外**
- ログイン側の `favorite_collections[]` はそのまま保持する（変更しない）

---

## 5. マージ後の更新処理

- ログイン側 `wishlist.data` を更新する
  - `items[]`：マージ後の配列
  - `favorite_collections[]`：既存のまま
  - `updated_at`：現在時刻（ISO8601）に更新
  - `version`：1 のまま

---

## 6. 引き継ぎ完了後のゲストデータ処理

- 引き継ぎが正常に完了した場合：
  - ゲスト側 localStorage の Wishlist データを削除する
- 引き継ぎが失敗した場合：
  - ゲスト側 localStorage は削除しない（再試行できるようにする）

---

## 7. エラー / 例外処理

### 7-1. API更新失敗（ログイン側の保存に失敗）
- UI上は「引き継ぎに失敗しました」等の簡易メッセージを表示
- localStorage は保持し、次回アクセスで再試行可能にする

### 7-2. 不正データ（variant_id欠損など）
- 欠損・不正な item はマージ対象から除外する
- コンソールに警告ログを出す（デバッグ用）

### 7-3. 削除済みvariant
- 表示時に除外（または削除済み表示）
- 必要に応じて将来クレンジング（v2検討）

---

## 8. 補足（v1の設計意図）
- v1では「お気に入り商品（variant）」と「お気に入りコレクション（Shopify Collection）」は独立して管理する
- items の分類（collection_ids 等）は v1では行わない
