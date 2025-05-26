# 문제 4 - JOIN

## 상품을 구매한 회원 비율 구하기

### **문제 설명**

다음은 어느 의류 쇼핑몰에 가입한 회원 정보를 담은 `USER_INFO` 테이블과 온라인 상품 판매 정보를 담은 `ONLINE_SALE` 테이블 입니다. `USER_INFO` 테이블은 아래와 같은 구조로 되어있으며 `USER_ID`, `GENDER`, `AGE`, `JOINED`는 각각 회원 ID, 성별, 나이, 가입일을 나타냅니다.

| Column name | Type | Nullable |
| --- | --- | --- |
| USER_ID | INTEGER | FALSE |
| GENDER | TINYINT(1) | TRUE |
| AGE | INTEGER | TRUE |
| JOINED | DATE | FALSE |

`GENDER` 컬럼은 비어있거나 0 또는 1의 값을 가지며 0인 경우 남자를, 1인 경우는 여자를 나타냅니다.

`ONLINE_SALE` 테이블은 아래와 같은 구조로 되어있으며 `ONLINE_SALE_ID`, `USER_ID`, `PRODUCT_ID`, `SALES_AMOUNT`, `SALES_DATE`는 각각 온라인 상품 판매 ID, 회원 ID, 상품 ID, 판매량, 판매일을 나타냅니다.

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

`USER_INFO` 테이블과 `ONLINE_SALE` 테이블에서 2021년에 가입한 전체 회원들 중 상품을 구매한 회원수와 상품을 구매한 회원의 비율(=2021년에 가입한 회원 중 상품을 구매한 회원수 / 2021년에 가입한 전체 회원 수)을 년, 월 별로 출력하는 SQL문을 작성해주세요. 상품을 구매한 회원의 비율은 소수점 두번째자리에서 반올림하고, 전체 결과는 년을 기준으로 오름차순 정렬해주시고 년이 같다면 월을 기준으로 오름차순 정렬해주세요.

---

### 예시

예를 들어 `USER_INFO` 테이블이 다음과 같고

| USER_ID | GENDER | AGE | JOINED |
| --- | --- | --- | --- |
| 1 | 1 | 26 | 2021-06-01 |
| 2 | NULL | NULL | 2021-06-25 |
| 3 | 0 | NULL | 2021-06-30 |
| 4 | 0 | 31 | 2021-07-03 |
| 5 | 1 | 25 | 2022-01-09 |
| 6 | 1 | 33 | 2022-02-14 |

`ONLINE_SALE` 이 다음과 같다면

| ONLINE_SALE_ID | USER_ID | PRODUCT_ID | SALES_AMOUNT | SALES_DATE |
| --- | --- | --- | --- | --- |
| 1 | 1 | 54 | 1 | 2022-01-01 |
| 2 | 1 | 3 | 2 | 2022-01-25 |
| 3 | 4 | 34 | 1 | 2022-01-30 |
| 4 | 6 | 253 | 3 | 2022-02-03 |
| 5 | 2 | 31 | 2 | 2022-02-09 |
| 6 | 5 | 35 | 1 | 2022-02-14 |
| 7 | 5 | 57 | 1 | 2022-02-18 |

2021년에 가입한 회원은 `USER_ID`가 1, 2, 3, 4 인 회원들로 총 4명 입니다. `ONLINE_SALE` 테이블에서 해당 회원들에 대한 판매 데이터는 다음과 같습니다.

| ONLINE_SALE_ID | USER_ID | PRODUCT_ID | SALES_AMOUNT | SALES_DATE |
| --- | --- | --- | --- | --- |
| 1 | 1 | 54 | 1 | 2022-01-01 |
| 2 | 1 | 3 | 2 | 2022-01-25 |
| 3 | 4 | 34 | 1 | 2022-01-30 |
| 5 | 2 | 31 | 2 | 2022-02-09 |

그러므로 년, 월 별로 상품을 구매한 회원수와 상품을 구매한 회원의 비율을 구하고 결과를 정렬하면 다음과 같아야 합니다.

| YEAR | MONTH | PURCHASED_USERS | PUCHASED_RATIO |
| --- | --- | --- | --- |
| 2022 | 1 | 2 | 0.5 |
| 2022 | 2 | 1 | 0.3 |
- 답
    
    ```sql
    SELECT 
        YEAR, 
        MONTH, 
        COUNT(DISTINCT O.USER_ID) AS PURCHASED_USERS, 
        ROUND(COUNT(DISTINCT O.USER_ID)/COUNT_TOT,1) AS PUCHASED_RATIO
    FROM 
        (
            SELECT *, COUNT(USER_ID) OVER() AS COUNT_TOT FROM USER_INFO WHERE YEAR(JOINED)='2021'
        ) AS U
        INNER JOIN 
        (
            SELECT *, YEAR(SALES_DATE) AS YEAR, MONTH(SALES_DATE) AS MONTH FROM ONLINE_SALE
        ) AS O
        ON O.USER_ID = U.USER_ID
    GROUP BY 1,2
    ORDER BY 1,2
    ```
    

---

## 🔍 쿼리 구성 해석

### 🔸 **1단계: USER_INFO 서브쿼리**

```sql
SELECT *, COUNT(USER_ID) OVER() AS COUNT_TOT
FROM USER_INFO
WHERE YEAR(JOINED) = '2021'
```

| 역할 | 설명 |
| --- | --- |
| `YEAR(JOINED) = '2021'` | 2021년에 가입한 유저만 필터링 |
| `COUNT(USER_ID) OVER()` | 전체 2021년 가입자 수를 **모든 row에 붙임** |

---

### 🔸 **2단계: ONLINE_SALE 서브쿼리**

```sql
SELECT *, YEAR(SALES_DATE) AS YEAR, MONTH(SALES_DATE) AS MONTH
FROM ONLINE_SALE
```

| 역할 | 설명 |
| --- | --- |
| `YEAR(SALES_DATE)` & `MONTH(SALES_DATE)` | 구매 일자 기준 연/월 분해 |
| 이 테이블과 위 USER 테이블을 **INNER JOIN** | 즉, **2021 가입자 중 실제 구매한 사람만 연결됨** |

---

### 🔸 **3단계: JOIN 후 그룹핑**

```sql
GROUP BY 1, 2
```

| 컬럼 | 의미 |
| --- | --- |
| `1` | `YEAR` |
| `2` | `MONTH` |

→ 월별로 집계

---

### 🔸 **4단계: SELECT 컬럼 해석**

```sql
SELECT
    YEAR,
    MONTH,
    COUNT(DISTINCT O.USER_ID) AS PURCHASED_USERS,
    ROUND(COUNT(DISTINCT O.USER_ID)/COUNT_TOT, 1) AS PURCHASED_RATIO
```

| 컬럼 | 의미 |
| --- | --- |
| `PURCHASED_USERS` | 해당 월에 실제로 구매한 유저 수 |
| `PURCHASED_RATIO` | 전체 2021 가입자 중 이 월에 구매한 사람의 비율 (%) |
| 예: 1000명 중 123명이 샀다면 → 12.3% |  |