# Wishlist App（variant + collection）

## 概要
本ドキュメントは、Shopifyストア向けに開発する  
**Wishlist（お気に入り）アプリ**の仕様・設計・実装・運用に関する情報を集約したものです。

本アプリは以下の特徴を持ちます。

- お気に入りは **variant単位**で保存
- **ゲストユーザー／ログインユーザー両対応**
- Shopifyテーマに直接組み込み、Theme App Extensionは v1 では使用しない
- サーバーサイドは Cloud Run を利用し、Shopify Admin API と連携する

---

## このREADMEの目的
- このアプリが「何をするものか」を一瞬で理解できるようにする
- 新しく関わる開発者・外注・将来の自分が迷わないようにする
- 「今回やらないこと」を明確にする

---

## 対象読者
- Shopifyテーマ実装担当
- バックエンド（Cloud Run / API）担当
- PM / 仕様策定担当
- 将来の保守・拡張担当者

---

## アプリのスコープ（v1）
### やること
- variant単位でのお気に入り登録・解除
- ゲストはブラウザ（localStorage）保存
- ログインユーザーはCustomerメタフィールド保存
- ログイン時にゲストのお気に入りを引き継ぐ

### やらないこと（v1では対象外）
- Theme App Extension 化
- 複数ストア配布
- 管理画面（Shopify Admin）用のUI提供
- レコメンド、通知、分析機能
- 未ログイン状態でのコレクション管理

---

## ドキュメント構成
- `01_Overview`：アプリの目的・思想・前提条件
- `02_Requirements`：機能要件・非機能要件
- `03_UX_UI`：画面仕様・ユーザーフロー
- `04_Data_Design`：JSONスキーマ・メタフィールド設計
- `05_API_Design`：Cloud Run API仕様
- `06_Auth_Security`：認証・セキュリティ設計
- `07_System_Architecture`：全体構成
- `08_Theme_Integration`：テーマ実装ルール
- `09_Operation`：運用・デプロイ
- `10_Test`：テスト観点
- `99_Future`：将来拡張

---

## バージョン管理
- 本ドキュメントは **v1** を前提とする
- 大きな仕様変更時は `version` を明示的に更新する

---

## 備考
- 本アプリは ENGAGER / ShopHub の設計思想を踏襲しつつ、
  単体でも再利用可能な構成を目指す
