# メタフィールド定義（Metafield Definition）

本ドキュメントでは、Wishlist App v1 において使用する  
**Shopify メタフィールドの定義一覧**をまとめる。

実装・運用・拡張における正の定義は本ドキュメントとする。

---

## 1. 方針

- Wishlistデータは **Customerメタフィールド**に集約する
- 共有可否などの商品制御は **Productメタフィールド**で管理する
- v1では Variantメタフィールドは使用しない
- 将来拡張を見据え、namespace はアプリ専用で統一する

---

## 2. Customer メタフィールド

### 2-1. Wishlist データ

#### 基本情報
| 項目 | 内容 |
|---|---|
| 対象 | Customer |
| Namespace | `wishlist` |
| Key | `data` |
| 型 | JSON（または single_line_text_field にJSON文字列） |
| 用途 | ログインユーザーのお気に入り・コレクション管理 |

#### データ内容（概要）
- お気に入りアイテム（variant単位）
- お気に入りコレクション（ログイン限定）
- アイテムとコレクションの紐づけ情報

#### 備考
- Shopify Admin 画面から内容確認は可能
- 管理画面からの直接編集は原則行わない（API経由更新）

---

## 3. Product メタフィールド

### 3-1. 共有可否フラグ（必須）

#### 定義
| 項目 | 内容 |
|---|---|
| 対象 | Product |
| Namespace | `custom` |
| Key | `share_enabled` |
| 型 | boolean |
| 初期値 | false（未設定時も false 扱い） |
| 用途 | 商品が「共有可能かどうか」の制御 |

#### 挙動
- `true`  
  - LINE共有 / リンクコピーなどの共有UIを表示する
- `false` または 未設定  
  - 共有UIを表示しない

#### 補足
- 共有単位は **variant** だが、許可判定は **product単位**
- 共有URLには `?variant=XXXX` を付与する

---

## 4. 使用しないメタフィールド（v1）

### 4-1. Variant メタフィールド
- v1では以下は使用しない
  - variant単位の共有可否
  - variant単位のwishlist補助情報

※ 将来、variantごとに共有制御が必要になった場合は  
`variant.metafields.custom.share_enabled` 等の追加を検討する。

---

## 5. 命名規則

### Namespace
- Wishlistアプリ固有データ：`wishlist`
- 汎用/商品制御系：`custom`

### Key
- snake_case を使用
- 意味が直感的に分かる名称とする

---

## 6. 運用ルール

- 新しいメタフィールドを追加する場合は、本ドキュメントに追記する
- 実装前に「Customer / Product / Variant のどれに持たせるか」を必ず明記する
- v1で不要なメタフィールドは追加しない（肥大化防止）

---

## 7. 関連ドキュメント
- `01_Overview/app_overview.md`
- `02_Requirements/functional_requirements.md`
- `04_Data_Design/wishlist_schema_customer.json`
- `03_UX_UI/screen_spec_pdp.md`
