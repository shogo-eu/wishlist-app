# 画面仕様：お気に入り一覧ページ（Wishlist）

本ドキュメントでは、Wishlist App v1 における  
**お気に入り一覧ページ（Wishlist Page）の画面仕様**を定義する。

---

## 1. 対象画面
- Shopify のページ（例：`/pages/wishlist`）

---

## 2. 対象ユーザー
- ゲストユーザー
- ログインユーザー（Customer）

---

## 3. ページ構成（レイアウト）

### 3-1. 共通ブロック
- お気に入り商品一覧（variant単位）
- 空状態（Empty State）

### 3-2. ログイン時のみ表示するブロック
- お気に入りコレクション一覧（Shopify Collection のお気に入り）
  - ユーザーが♡登録したコレクションバナーを表示する

---

## 4. お気に入り商品（variant）一覧

### 4-1. 表示単位
- 表示単位は **variant**
- 主キー：`variant_id`

### 4-2. アイテム操作（共通）
- お気に入り解除（削除）
- 商品詳細への遷移（`?variant=...` 付き）

---

## 5. お気に入りコレクション（ログイン限定）

### 5-1. 定義
- ストア側で用意した Shopify Collection を、ユーザーが♡登録する機能
- ユーザーが作るフォルダ機能は v1 では提供しない

### 5-2. 表示（Wishlistページ）
- `wishlist.data.favorite_collections[]` に登録されているコレクションを一覧表示する
- 表示要素：
  - コレクションバナー画像（または代表画像）
  - コレクション名
  - コレクションへのリンク（例：`/collections/{handle}`）

### 5-3. 解除
- Wishlistページ上でもコレクションの♡解除ができる（任意だが推奨）
- 解除すると一覧から消える

---

## 6. コレクション一覧セクション（別画面/別セクションでの登録）

### 6-1. UI要件
- コレクション一覧（バナー群）に♡ボタンを付与する
- ♡はトグル（登録/解除）で動作する

### 6-2. 利用条件
- ログインユーザーのみ登録/解除可能
- ゲストは♡を非表示、またはクリック時にログイン誘導を表示する

### 6-3. 保存
- 保存先：Customer metafield `wishlist.data.favorite_collections`
- 保存単位：`collection_id`（推奨）＋可能なら `handle` を併記

---

## 7. 共有機能（商品：条件付き）
- 商品共有は `product.metafields.custom.share_enabled == true` の場合のみ表示
- 共有URLは商品詳細URL + `?variant=...`

---

## 8. 空状態（Empty State）

### 8-1. お気に入り商品が0件
- 「お気に入りはまだありません」
- 「気になる商品を♡で保存できます」

### 8-2. ログイン誘導（ゲスト）
- 「ログインするとコレクションもお気に入り登録できます」

---

## 9. 非対応（v1）
- ユーザー作成のフォルダ（コレクション作成/編集）
- お気に入り商品のフォルダ分類（item⇔folder紐づけ）
- コレクションの共有（必要ならv2で検討）

---

## 10. 関連ドキュメント
- `02_Requirements/functional_requirements.md`
- `04_Data_Design/wishlist_schema_customer.json`
- `04_Data_Design/wishlist_schema_guest.json`
- `04_Data_Design/metafield_definition.md`
- `03_UX_UI/screen_spec_pdp.md`
