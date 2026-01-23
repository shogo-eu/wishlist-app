# メタフィールド定義（Metafield Definition）

本ドキュメントでは、Wishlist App v1 において使用する  
**Shopify メタフィールドの定義一覧**をまとめる。

本ドキュメントは、  
**「どのデータを、どのメタフィールドに保存するか」** に関する  
正の定義（Single Source of Truth）とする。

※ 各メタフィールドに保存される **JSON構造の正** は  
`wishlist_schema_customer.json` / `wishlist_schema_guest.json` を正とする。

---

## 1. 方針

- Wishlistデータは **Customerメタフィールドに集約**する
- 共有可否などの商品制御は **Productメタフィールド**で管理する
- v1では **Variantメタフィールドは使用しない**
- 将来拡張を見据え、**namespace は機能単位で分離**する

---

## 2. Customer メタフィールド

### 2-1. Wishlist データ（主要）

#### 基本情報
| 項目 | 内容 |
|---|---|
| 対象 | Customer |
| Namespace | `wishlist` |
| Key | `data` |
| 型 | JSON（`json` または `single_line_text_field` にJSON文字列） |
| 用途 | ログインユーザーのお気に入り・コレクション管理 |

#### データ内容（概要）
- お気に入りアイテム（variant単位）
- お気に入りコレクション  
  （Shopify Collection のお気に入り：`favorite_collections`）
- （v1では）アイテム分類は行わない

※ 詳細なJSON構造は  
`04_Data_Design/wishlist_schema_customer.json` を正とする。

#### 運用上の注意
- Shopify Admin 画面から内容確認は可能
- **管理画面からの直接編集は原則禁止**（必ずAPI経由で更新）
- 更新時は `updated_at` を必ず更新する

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
| 用途 | 商品が「外部共有可能かどうか」の制御 |

#### 挙動
- `true`
  - LINE共有 / リンクコピーなどの共有UIを表示する
- `false` または 未設定
  - 共有UIを表示しない

#### 補足
- 共有単位は **variant** とする
- ただし、**許可判定は product 単位**で行う
- 共有URLには `?variant=XXXX` を付与する

---

## 4. 使用しないメタフィールド（v1）

### 4-1. Variant メタフィールド
v1では以下の用途では Variantメタフィールドを使用しない。

- variant単位の共有可否制御
- variant単位のWishlist補助情報保存

#### 理由
- Wishlistの主キーは `variant_id` であり、  
  データは Customer 側に集約した方が管理・拡張しやすいため
- 商品側の制御は product 単位で十分と判断したため

※ 将来、variantごとに共有可否や制御が必要になった場合は  
`variant.metafields.custom.share_enabled` 等の追加を検討する。

---

## 5. 命名規則

### Namespace
- Wishlistアプリ固有データ：`wishlist`
- 汎用 / 商品制御系：`custom`

### Key
- snake_case を使用する
- 意味が直感的に分かる名称とする
- 省略形・暗号的な命名は避ける

---

## 6. 運用ルール

- 新しいメタフィールドを追加する場合は、**必ず本ドキュメントに追記**する
- 実装前に以下を明記する
  - 対象（Customer / Product / Variant）
  - 保存理由（なぜそこに持たせるのか）
- v1で不要なメタフィールドは追加しない（肥大化防止）

---

## 7. 関連ドキュメント
- `01_Overview/app_overview.md`
- `02_Requirements/functional_requirements.md`
- `04_Data_Design/wishlist_schema_customer.json`
- `04_Data_Design/wishlist_schema_guest.json`
- `03_UX_UI/screen_spec_pdp.md`
