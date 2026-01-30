# 画面仕様：商品詳細ページ（PDP）

本ドキュメントでは、Wishlist App v1 における  
**商品詳細ページ（Product Detail Page / PDP）の画面仕様**を定義する。

本仕様は、テーマ実装・JS実装・API連携の正とする。

---

## 1. 対象画面
- Shopify 商品詳細ページ
- テーマ：既存テーマ（Theme App Extension は使用しない）

---

## 2. 対象ユーザー
- ゲストユーザー
- ログインユーザー（Customer）

※ 両者で基本UIは共通だが、保存先・一部挙動が異なる。

---

## 3. 表示要素一覧

### 3-1. Wishlist（お気に入り）ボタン
- ハートアイコン形式
- variant単位で状態を切り替える
- 商品価格・購入ボタン付近に配置することを推奨

### 3-2. 共有ボタン（条件付き）
- LINE共有
- リンクコピー
- 表示条件：`product.metafields.custom.share_enabled == true`

---

## 4. Wishlistボタン仕様（ハート）

### 4-1. 表示状態
| 状態 | 表示 |
|---|---|
| 未保存 | ハートOFF |
| 保存済 | ハートON（active） |

### 4-2. 保存単位
- 保存単位は **variant**
- 判定キー：`variant_id`

---

## 5. ユーザー別挙動

### 5-1. ゲストユーザー

#### 保存先
- localStorage（`wishlist_schema_guest.json`）

#### 操作時の挙動
- ハートクリックで即時ON/OFF切替
- ページリロード後も状態は保持される

#### 制限
- コレクション機能は利用不可
- 共有ボタンは利用可能（商品が共有可の場合）

---

### 5-2. ログインユーザー

#### 保存先
- Customer メタフィールド：`wishlist.data`

#### 操作時の挙動
- ハートクリック時に API を呼び出す
- API成功後に状態を反映
- API失敗時は状態を元に戻し、エラーメッセージを表示

---

## 6. Variant切替時の挙動（重要）

### 6-1. 状態再評価
- variant変更時に、選択中variantの `variant_id` を取得
- Wishlist保存状態を再判定し、ハート表示を更新する

### 6-2. 保存済み判定
- ゲスト：localStorage内の `items[].variant_id`
- ログイン：取得済み `wishlist.data.items[].variant_id`

---

## 7. 共有ボタン仕様

### 7-1. 表示条件
- `product.metafields.custom.share_enabled == true` の場合のみ表示

### 7-2. 共有単位
- **variant単位**で共有する

### 7-3. 共有URL
- 商品詳細ページURLを使用
- variant指定をクエリで付与する

例：/products/sample-product?variant=1234567890


---

### 7-4. 共有方法（v1）
- LINE共有
  - LINE公式共有URLスキームを使用
- リンクコピー
  - クリップボードにURLをコピー

---

## 8. エラーハンドリング（PDP）

### 8-1. APIエラー（ログイン時）
- Wishlist更新APIが失敗した場合：
  - ハート状態を元に戻す
  - ユーザーに簡易エラーメッセージを表示

### 8-2. データ不整合
- variant情報が取得できない場合：
  - Wishlist操作を無効化
  - コンソールに警告ログを出力

---

## 9. パフォーマンス配慮

- 初期表示時に wishlist.data を一度だけ取得する
- variant切替時は再取得せず、ローカル状態で判定する
- API呼び出しは最小限とする（トグル操作時のみ）

---

## 10. 非対応（v1）

- PDP上でのコレクション分類UI
- お気に入りメモ・コメント入力
- 共有履歴・回数の表示

---

## 11. 関連ドキュメント
- `02_Requirements/functional_requirements.md`
- `04_Data_Design/wishlist_schema_customer.json`
- `04_Data_Design/wishlist_schema_guest.json`
- `04_Data_Design/metafield_definition.md`
