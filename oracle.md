# Oracle 独自関数まとめ

## 1. NULL 処理

### ■ NVL

NULL を別の値に置き換える。

構文:  
NVL(列名, 置き換える値)

例:

```sql
SELECT NVL(GRADE, 0) FROM STUDENTS;
```

---

## 2. 条件分岐

### ■ DECODE（Oracle 独自）

IF のような条件分岐。

構文:  
DECODE(列, 値 1, 結果 1, 値 2, 結果 2, デフォルト値)

例:

```sql
SELECT NAME,
       DECODE(GRADE,
              100, '満点',
              80,  '80点',
              'その他') AS COMMENT
FROM STUDENTS;
```

※ 現在は CASE 式の使用が一般的

---

## 3. 行制限

### ■ ROWNUM

取得行数を制限する。

例:

```sql
SELECT * FROM STUDENTS
WHERE ROWNUM <= 5;
```

---

## 4. 階層問い合わせ

### ■ CONNECT BY

階層データや連番生成に使用。

例:

```sql
SELECT LEVEL
FROM dual
CONNECT BY LEVEL <= 10;
```

1〜10 の数値を生成できる。

---

## 5. 文字列連結

### ■ LISTAGG

複数行を 1 行にまとめる。

例:

```sql
SELECT LISTAGG(NAME, ', ')
       WITHIN GROUP (ORDER BY NAME)
FROM STUDENTS;
```

---

### ■ WITHIN GROUP

LISTAGG や統計関数で順序を指定する際に使用。

例:

```sql
SELECT LISTAGG(NAME, '; ')
       WITHIN GROUP (ORDER BY GRADE DESC)
FROM STUDENTS;
```

---

## 6. 日付関数

### ■ ADD_MONTHS

指定月数を加算。

例:

```sql
SELECT ADD_MONTHS(SYSDATE, 3) FROM dual;
```

### ■ NEXT_DAY

次の指定曜日を取得。

例:

```sql
SELECT NEXT_DAY(SYSDATE, 'MONDAY') FROM dual;
```

---

## 7. 乱数生成

### ■ DBMS_RANDOM

乱数を生成するパッケージ。

例:

```sql
SELECT DBMS_RANDOM.VALUE FROM dual;
```

---

## 8. 分析関数（ウィンドウ関数）

### ■ RANK

順位を付ける（同順位あり・順位は飛ぶ）

例:

```sql
SELECT NAME,
       GRADE,
       RANK() OVER (ORDER BY GRADE DESC) AS RANK_NO
FROM STUDENTS;
```

### ■ DENSE_RANK

順位を付ける（同順位あり・順位は飛ばない）

例:

```sql
SELECT NAME,
       GRADE,
       DENSE_RANK() OVER (ORDER BY GRADE DESC) AS DENSE_RANK_NO
FROM STUDENTS;
```

### ■ ROW_NUMBER

強制的に連番を振る（同点でも重複しない）

例:

```sql
SELECT NAME,
       GRADE,
       ROW_NUMBER() OVER (ORDER BY GRADE DESC) AS ROW_NO
FROM STUDENTS;
```

### ■ PARTITION BY を使う例

グループごとに順位を付ける。

例:

```sql
SELECT NAME,
       GRADE,
       RANK() OVER (PARTITION BY GRADE ORDER BY ID) AS RANK_IN_GRADE
FROM STUDENTS;
```

---

## 9. EXISTS

行が存在するかを確認するサブクエリで使用。

例:

```sql
SELECT NAME
FROM STUDENTS S
WHERE EXISTS (
    SELECT 1
    FROM STUDENTS
    WHERE GRADE > 90 AND ID = S.ID
);
```

---

## 10. UNION

複数の SELECT 結果を結合（重複行は削除）。

例:

```sql
SELECT NAME, GRADE FROM STUDENTS WHERE GRADE >= 90
UNION
SELECT NAME, GRADE FROM STUDENTS WHERE GRADE <= 10;
```

※ UNION ALL は重複を削除しない

---

# よく使う関数ランキング（実務目安）

1. NVL
2. RANK / ROW_NUMBER
3. LISTAGG / WITHIN GROUP
4. ADD_MONTHS
5. DECODE / EXISTS
6. UNION

---

# 補足

- 分析関数は GROUP BY と違い、行を消さずに集計可能
- PARTITION BY はグループ単位で分析するために使用
- 最近の SQL では ANSI 標準構文（CASE, COALESCE, FETCH FIRST など）も推奨される

# Oracle LIKE パターン検索例 20 選

-- 1. 特定文字で始まる
SELECT \* FROM employees WHERE name LIKE 'S%';

-- 2. 特定文字で終わる
SELECT \* FROM employees WHERE name LIKE '%n';

-- 3. 特定文字を含む
SELECT \* FROM employees WHERE name LIKE '%ar%';

-- 4. 任意の 1 文字を指定
SELECT \* FROM employees WHERE name LIKE 'J_n';

-- 5. 文字列の長さを指定
SELECT \* FROM employees WHERE name LIKE '\_\_\_';

-- 6. 文字列の先頭と末尾を固定
SELECT \* FROM employees WHERE name LIKE 'S%n';

-- 7. 特殊文字 % を検索
SELECT \* FROM discounts WHERE description LIKE '100\%%' ESCAPE '\';

-- 8. 特殊文字 \_ を検索
SELECT \* FROM codes WHERE code LIKE 'A_1' ESCAPE '\';

-- 9. 複数文字の任意マッチ
SELECT \* FROM products WHERE product_name LIKE '%Pro%';

-- 10. 先頭 1 文字だけ任意
SELECT \* FROM employees WHERE name LIKE '\_ohn';

-- 11. 大文字・小文字を区別しない（UPPER 使用）
SELECT \* FROM employees WHERE UPPER(name) LIKE 'S%';

-- 12. 部分文字列の複数パターン
SELECT \* FROM employees WHERE name LIKE 'J%n' OR name LIKE 'K%n';

-- 13. 任意文字列の位置指定
SELECT \* FROM employees WHERE name LIKE 'A\_\_n';

-- 14. REGEXP_LIKE で複数文字候補
SELECT \* FROM employees WHERE REGEXP_LIKE(name, '^J[aeiou]n$');

-- 15. 数字を含む文字列
SELECT \* FROM products WHERE product_code LIKE '%1%';

-- 16. 特定文字以外を検索（REGEXP_LIKE）
SELECT \* FROM employees WHERE REGEXP_LIKE(name, '^[^A]%');

-- 17. 前後に任意文字
SELECT \* FROM employees WHERE name LIKE '%a%l%';

-- 18. 空白を含む文字列
SELECT \* FROM employees WHERE name LIKE '% %';

-- 19. 複数ワイルドカード併用
SELECT \* FROM products WHERE product_name LIKE 'S%\_Pro%';

-- 20. LIKE と ESCAPE の組み合わせ
SELECT \* FROM paths WHERE path LIKE 'C:\Windows\%' ESCAPE '\';

# Oracle 正規表現リファレンス

---

## 1. 正規表現関数

| 関数                                           | 説明                                                       |
| ---------------------------------------------- | ---------------------------------------------------------- |
| `REGEXP_LIKE(column, 'pattern')`               | 文字列が正規表現パターンにマッチするかを判定（TRUE/FALSE） |
| `REGEXP_INSTR(column, 'pattern')`              | パターンが文字列に現れる位置を返す（見つからない場合は 0） |
| `REGEXP_SUBSTR(column, 'pattern')`             | パターンにマッチした部分文字列を返す                       |
| `REGEXP_REPLACE(column, 'pattern', 'replace')` | パターンにマッチした部分文字列を置換                       |
| `REGEXP_COUNT(column, 'pattern')`              | パターンにマッチする箇所の数を返す（Oracle 11g 以降）      |

---

## 2. 主なメタ文字・構文

| メタ文字 | 説明                                                |
| -------- | --------------------------------------------------- | ------------ | ------ |
| `.`      | 任意の 1 文字                                       |
| `^`      | 文字列の先頭                                        |
| `$`      | 文字列の末尾                                        |
| `*`      | 直前の文字の 0 回以上の繰り返し                     |
| `+`      | 直前の文字の 1 回以上の繰り返し                     |
| `?`      | 直前の文字の 0 または 1 回                          |
| `{n}`    | 直前の文字のちょうど n 回                           |
| `{n,}`   | 直前の文字の n 回以上                               |
| `{n,m}`  | 直前の文字の n 回以上 m 回以下                      |
| `[]`     | 文字クラス（例: `[a-z]` は a ～ z の任意の 1 文字） |
| `[^]`    | 否定文字クラス（例: `[^0-9]` は数字以外の 1 文字）  |
| `        | `                                                   | OR（例: `cat | dog`） |
| `()`     | グループ化（キャプチャ）                            |
| `\\d`    | 数字 `[0-9]`                                        |
| `\\D`    | 数字以外 `[^0-9]`                                   |
| `\\s`    | 空白文字（スペース、タブ、改行など）                |
| `\\S`    | 空白文字以外                                        |
| `\\w`    | 英数字＋アンダースコア `[A-Za-z0-9_]`               |
| `\\W`    | 英数字＋アンダースコア以外                          |

> 注意: SQL 内では `\\` を使ってエスケープする必要があります。

---

## 3. 使用例

```sql
-- メールアドレスの形式チェック
SELECT email
FROM users
WHERE REGEXP_LIKE(email, '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$');

-- 数字だけ抽出
SELECT REGEXP_SUBSTR('注文番号:12345', '\\d+') AS number
FROM dual;

-- ハイフンを除去
SELECT REGEXP_REPLACE('090-1234-5678', '-', '') AS phone
FROM dual;

-- 特定の文字列の出現回数
SELECT REGEXP_COUNT('abc abc abc', 'abc') AS count
FROM dual;
```

# データベースのキーと制約まとめ

---

## 1. PK（Primary Key：主キー）

- **意味**：テーブル内で各レコードを一意に識別する列（または列の組み合わせ）
- **特徴**：

  - 値が **重複しない**
  - 値が **NULL にならない**
  - テーブルに必ず **1 つだけ存在**

- **例**：ユーザーテーブル
  | user_id (PK) | name | email |
  |--------------|--------|-----------------|
  | 1 | Alice | alice@example.com |
  | 2 | Bob | bob@example.com |

---

## 2. UK（Unique Key：ユニークキー）

- **意味**：テーブル内で **重複を許さない列**（複数列の組み合わせでも可）
- **特徴**：

  - 値が **重複しない**
  - PK との違いは、**NULL を許す場合がある**
  - 1 テーブルに **複数設定できる**

- **例**：ユーザーテーブルのメールアドレス
  | user_id (PK) | name | email (UK) |
  |--------------|--------|-----------------|
  | 1 | Alice | alice@example.com |
  | 2 | Bob | bob@example.com |

---

## 3. FK（Foreign Key：外部キー）

- **意味**：他のテーブルの **PK や UK を参照**する列。テーブル間の関係を作るために使用。
- **特徴**：

  1. 参照先の値しか入れられない
  2. 参照先レコードが削除された時の挙動を設定可能
     - `CASCADE`：関連するレコードも削除
     - `SET NULL`：値を NULL にする
     - `NO ACTION` / `RESTRICT`：削除を禁止
  3. 1 テーブルに複数の FK を設定可能

- **例**：ユーザーと注文の関係
  **ユーザーテーブル**
  | user_id (PK) | name |
  |--------------|--------|
  | 1 | Alice |
  | 2 | Bob |

**注文テーブル**
| order_id (PK) | user_id (FK) | item |
|---------------|--------------|--------|
| 101 | 1 | Book |
| 102 | 2 | Pen |

---

## 4. CK（Check Constraint：チェック制約）

- **意味**：列や複数列の値が **条件に合致するかどうかを制約**する仕組み
- **特徴**：

  1. 値の範囲やパターンを制御可能
  2. INSERT/UPDATE 時に条件を満たさないとエラーになる
  3. PK や UK とは異なり、重複や参照関係とは無関係

- **例**：社員テーブルで年齢 18 歳以上
  | emp_id (PK) | name | age |
  |------------|--------|-----|
  | 1 | Alice | 25 |
  | 2 | Bob | 30 |

```sql
ALTER TABLE employee
ADD CONSTRAINT chk_age CHECK (age >= 18);
```

# PK/UK/FK/CK の違いまとめ

| 項目 | PK（主キー）         | UK（ユニークキー）   | FK（外部キー）               | CK（チェック制約）       |
| ---- | -------------------- | -------------------- | ---------------------------- | ------------------------ |
| 用途 | レコード識別         | 値の重複防止         | 他テーブルとの整合性         | 値が条件を満たすかの制約 |
| 重複 | ×                    | ×                    | ○（参照先に依存）            | ○（条件次第）            |
| NULL | ×                    | ○                    | ○（参照先に依存）            | 条件次第                 |
| 対象 | 列（単一または複合） | 列（単一または複合） | 他テーブルの PK/UK 列        | 列または複数列           |
| 挙動 | 自動識別             | 重複制限             | 削除・更新時の整合性制御可能 | 条件を満たさないとエラー |

---

💡 **ポイント**

- **PK** = 「身分証明書」
- **UK** = 「ビジネスルールでのユニーク制約」
- **FK** = 「他テーブルとの関係を保持」
- **CK** = 「値が正しいかのルール」
