# 문제 4 - GROUP BY

## 년, 월, 성별 별 상품 구매 회원 수 구하기

### **문제 설명**

다음은 어느 의류 쇼핑몰에 가입한 회원 정보를 담은 `USER_INFO` 테이블과 온라인 상품 판매 정보를 담은 `ONLINE_SALE` 테이블 입니다.`USER_INFO` 테이블은 아래와 같은 구조로 되어있으며 `USER_ID`, `GENDER`, `AGE`, `JOINED`는 각각 회원 ID, 성별, 나이, 가입일을 나타냅니다.

| Column name | Type | Nullable |
| --- | --- | --- |
| USER_ID | INTEGER | FALSE |
| GENDER | TINYINT(1) | TRUE |
| AGE | INTEGER | TRUE |
| JOINED | DATE | FALSE |

`GENDER` 컬럼은 비어있거나 0 또는 1의 값을 가지며 0인 경우 남자를, 1인 경우는 여자를 나타냅니다.

`ONLINE_SALE` 테이블은 아래와 같은 구조로 되어있으며, `ONLINE_SALE_ID`, `USER_ID`, `PRODUCT_ID`, `SALES_AMOUNT`, `SALES_DATE`는 각각 온라인 상품 판매 ID, 회원 ID, 상품 ID, 판매량, 판매일을 나타냅니다.

| Column name | Type | Nullable |
| --- | --- | --- |
| ONLINE_SALE_ID | INTEGER | FALSE |
| USER_ID | INTEGER | FALSE |
| PRODUCT_ID | INTEGER | FALSE |
| SALES_AMOUNT | INTEGER | FALSE |
| SALES_DATE | DATE | FALSE |

동일한 날짜, 회원 ID, 상품 ID 조합에 대해서는 하나의 판매 데이터만 존재합니다.

---

### 문제

`USER_INFO` 테이블과 `ONLINE_SALE` 테이블에서 년, 월, 성별 별로 상품을 구매한 회원수를 집계하는 SQL문을 작성해주세요. 결과는 년, 월, 성별을 기준으로 오름차순 정렬해주세요. 이때, 성별 정보가 없는 경우 결과에서 제외해주세요.

---

### 예시

예를 들어 `USER_INFO` 테이블이 다음과 같고

| USER_ID | GENDER | AGE | JOINED |
| --- | --- | --- | --- |
| 1 | 1 | 26 | 2021-06-01 |
| 2 | NULL | NULL | 2021-06-25 |
| 3 | 0 | NULL | 2021-06-30 |
| 4 | 0 | 31 | 2021-07-03 |
| 5 | 1 | 25 | 2021-07-09 |
| 6 | 1 | 33 | 2021-07-14 |

`ONLINE_SALE` 테이블이 다음과 같다면

| ONLINE_SALE_ID | USER_ID | PRODUCT_ID | SALES_AMOUNT | SALES_DATE |
| --- | --- | --- | --- | --- |
| 1 | 1 | 54 | 1 | 2022-01-01 |
| 2 | 1 | 3 | 2 | 2022-01-25 |
| 3 | 4 | 34 | 1 | 2022-01-30 |
| 4 | 6 | 253 | 3 | 2022-02-03 |
| 5 | 2 | 31 | 2 | 2022-02-09 |
| 6 | 5 | 35 | 1 | 2022-02-14 |
| 7 | 5 | 57 | 1 | 2022-02-18 |

2022년 1월에 상품을 구매한 회원은 `USER_ID` 가 1(`GENDER`=1), 4(`GENDER`=0)인 회원들이고,

2022년 2월에 상품을 구매한 회원은 `USER_ID` 가 2(`GENDER`=NULL), 5(`GENDER`=1), 6(`GENDER`=1)인 회원들 이므로,

년, 월, 성별 별로 상품을 구매한 회원수를 집계하고, 년, 월, 성별을 기준으로 오름차순 정렬하면 다음과 같은 결과가 나와야 합니다.

| YEAR | MONTH | GENDER | USERS |
| --- | --- | --- | --- |
| 2022 | 1 | 0 | 1 |
| 2022 | 1 | 1 | 1 |
| 2022 | 2 | 1 | 2 |

- 답
    
    ```sql
    SELECT DATE_FORMAT(SALES_DATE, "%Y") AS YEAR, MONTH(SALES_DATE) AS MONTH, UI.GENDER, COUNT(DISTINCT OS.USER_ID) AS USERS
    FROM USER_INFO UI
    JOIN ONLINE_SALE OS ON UI.USER_ID = OS.USER_ID
    WHERE GENDER IS NOT NULL
    GROUP BY YEAR, MONTH, GENDER
    ORDER BY YEAR, MONTH, GENDER
    ```
    

---

## ✅ 쿼리 기능 설명

```sql
SELECT
  **DATE_FORMAT(SALES_DATE, "%Y") AS YEAR,
  MONTH(SALES_DATE) AS MONTH,**
  UI.GENDER,
  COUNT(DISTINCT OS.USER_ID) AS USERS
FROM USER_INFO UI
JOIN ONLINE_SALE OS ON UI.USER_ID = OS.USER_ID
WHERE GENDER IS NOT NULL
GROUP BY YEAR, MONTH, GENDER
ORDER BY YEAR, MONTH, GENDER
```

| 항목 | 설명 |
| --- | --- |
| `JOIN` | 유저별 성별 정보 + 매출 데이터를 연결 |
| `WHERE GENDER IS NOT NULL` | 성별이 존재하는 유저만 필터링 |
| `GROUP BY` | 연도, 월, 성별별로 집계 |
| `COUNT(DISTINCT OS.USER_ID)` | 중복된 유저 구매 제거해서 **순 유저 수 카운트** |

---

## 💡 성능 + 구조 개선 포인트

### ✅ 1. `DATE_FORMAT`과 `MONTH()`는 인덱스를 무력화할 수 있음

> SALES_DATE에 인덱스가 있더라도 DATE_FORMAT()이나 MONTH()를 쓰면
> 
> 
> **MySQL 인덱스 범위 스캔이 안 될 수 있어!**
> 

### 🔧 개선 예시:

```sql
SELECT
  **YEAR(OS.SALES_DATE) AS YEAR,
  MONTH(OS.SALES_DATE) AS MONTH,**
  UI.GENDER,
  COUNT(DISTINCT OS.USER_ID) AS USERS
FROM USER_INFO UI
JOIN ONLINE_SALE OS ON UI.USER_ID = OS.USER_ID
WHERE UI.GENDER IS NOT NULL
GROUP BY YEAR, MONTH, UI.GENDER
ORDER BY YEAR, MONTH, UI.GENDER;
```

👉 `DATE_FORMAT()` 대신 `YEAR()`와 `MONTH()`로 변경해서 **인덱스 우회 최소화**

---

### ✅ 2. alias 쓰는 위치 주의

`GROUP BY YEAR, MONTH, GENDER` ← 이건 기능은 OK지만,

MySQL에서는 **컬럼 번호, alias, 원본 컬럼명**을 섞어 쓰면 모호해질 수 있음

→ `GROUP BY`에는 **명확히 `YEAR(OS.SALES_DATE)` 같은 원본 기준** 쓰는 게 더 안전함

---

### ✅ 3. 인덱스 확인하면 좋을 것들

| 테이블 | 추천 인덱스 |
| --- | --- |
| `USER_INFO` | `USER_ID`, `GENDER` 복합 인덱스 |
| `ONLINE_SALE` | `USER_ID`, `SALES_DATE` 복합 인덱스 |

→ 이렇게 하면 `JOIN`과 `WHERE`, `GROUP BY` 성능 향상 가능!