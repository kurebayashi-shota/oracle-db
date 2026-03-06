# 属性（カラム）の設計（テーブルの詳細化）

## 目的

テーブルごとに **どの情報を管理するか** を具体的に決める段階です。

- 主キー、外部キー、制約もここで考える

---

## 使用できるツール

- **スプレッドシート**（Excel, Google Sheets）

  - 「テーブル名」「カラム名」「データ型」「制約」「備考」を整理すると便利

- **テキストエディタ**（VS Code, Sublime Text など）
  - マークダウン形式で整理可能

---

## 作業手順

### 1. 各テーブルに必要なカラムを洗い出す

**users（ユーザー情報）**

- id → 主キー、一意
- name → ユーザー名
- email → メールアドレス（ユニーク制約）
- created_at → 登録日時

**products（商品情報）**

- id → 主キー
- name → 商品名
- price → 価格
- stock → 在庫数

**orders（注文情報）**

- id → 主キー
- user_id → 外部キー（users.id）
- order_date → 注文日時
- status → 注文ステータス（例：発送済み、キャンセル）

**order_items（注文商品中間テーブル）**

- id → 主キー
- order_id → 外部キー（orders.id）
- product_id → 外部キー（products.id）
- quantity → 注文数
- price → 注文時の単価

---

### 2. 制約やデータ型を検討

- 主キー（PK）、外部キー（FK）を明確に
- NULL 許可／必須の検討
- ユニーク制約、デフォルト値なども記入

例（スプレッドシート風に整理）:

| テーブル名 | カラム名   | データ型     | 制約             | 備考     |
| ---------- | ---------- | ------------ | ---------------- | -------- |
| users      | id         | INT          | PK, NOT NULL     |          |
| users      | name       | VARCHAR(100) | NOT NULL         |          |
| users      | email      | VARCHAR(150) | NOT NULL, UNIQUE |          |
| users      | created_at | DATETIME     | NOT NULL         | 登録日時 |

> 他テーブルも同様に整理していく

---

## 💡 ポイント

- 主キーと外部キーの設定は必須
- テーブル間の関連性を意識してカラムを決める
  - 例：`orders.user_id` は `users.id` を参照する
- NULL 許可や制約も同時にメモしておく

> これが次の ER 図作成や SQL 作成の基礎になる

# 設計例

## 各テーブルに必要なカラムを洗い出す

**persons(個人情報)**

- id -> 主キー
- family_id -> 所属
- name -> 名
- birth ->　生年月日
- sex -> 性別
- marriage -> 既婚:family_id が入る,未婚:NULL
- parent -> 親フラグ
- patriarch -> 家長フラグ

**family(家族情報)**

- id -> 主キー
- name -> 姓
- address -> 住所

**history(所属履歴)**

- id -> 主キー
- person_id -> 誰が
- family_id -> どこにいったか

## 制約やデータ型を検討

- persons テーブル

| カラム名  | データ型    | 制約         | 備考 |
| --------- | ----------- | ------------ | ---- |
| id        | NUMBER      | PK, NOT NULL |      |
| name      | VARCHAR(16) | NOT NULL     |      |
| family_id | NUMBER      | NOT NULL     |      |
| birth     | DATE        | NOT NULL     |      |
| sex       | NUMBER(1)   |              |      |
| marriage  | NUMBER      |              |      |
| parent    | NUMBER(1)   |              |      |
| patriarch | NUMBER(1)   |              |      |

- familys テーブル

| カラム名 | データ型           | 制約         | 備考 |
| -------- | ------------------ | ------------ | ---- |
| id       | NUMBER             | PK, NOT NULL |      |
| name     | VARCHAR(16)        | NOT NULL     |      |
| address  | VARCHAR2(150 CHAR) | NOT NULL     |      |

- history

| カラム名  | データ型 | 制約         | 備考 |
| --------- | -------- | ------------ | ---- |
| id        | NUMBER   | PK, NOT NULL |      |
| person_id | NUMBER   | NOT NULL     |      |
| family_id | NUMBER   | NOT NULL     |      |
