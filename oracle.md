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
