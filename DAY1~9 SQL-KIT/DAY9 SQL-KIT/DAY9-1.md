# 문제 1 - JOIN

## 5월 식품들의 총매출 조회하기

### **문제 설명**

다음은 식품의 정보를 담은 `FOOD_PRODUCT` 테이블과 식품의 주문 정보를 담은 `FOOD_ORDER` 테이블입니다. `FOOD_PRODUCT` 테이블은 다음과 같으며 `PRODUCT_ID`, `PRODUCT_NAME`, `PRODUCT_CD`, `CATEGORY`, `PRICE`는 식품 ID, 식품 이름, 식품코드, 식품분류, 식품 가격을 의미합니다.

| Column name | Type | Nullable |
| --- | --- | --- |
| PRODUCT_ID | VARCHAR(10) | FALSE |
| PRODUCT_NAME | VARCHAR(50) | FALSE |
| PRODUCT_CD | VARCHAR(10) | TRUE |
| CATEGORY | VARCHAR(10) | TRUE |
| PRICE | NUMBER | TRUE |

`FOOD_ORDER` 테이블은 다음과 같으며 `ORDER_ID`, `PRODUCT_ID`, `AMOUNT`, `PRODUCE_DATE`, `IN_DATE`, `OUT_DATE`, `FACTORY_ID`, `WAREHOUSE_ID`는 각각 주문 ID, 제품 ID, 주문량, 생산일자, 입고일자, 출고일자, 공장 ID, 창고 ID를 의미합니다.

| Column name | Type | Nullable |
| --- | --- | --- |
| ORDER_ID | VARCHAR(10) | FALSE |
| PRODUCT_ID | VARCHAR(5) | FALSE |
| AMOUNT | NUMBER | FALSE |
| PRODUCE_DATE | DATE | TRUE |
| IN_DATE | DATE | TRUE |
| OUT_DATE | DATE | TRUE |
| FACTORY_ID | VARCHAR(10) | FALSE |
| WAREHOUSE_ID | VARCHAR(10) | FALSE |

---

### 문제

`FOOD_PRODUCT`와 `FOOD_ORDER` 테이블에서 생산일자가 2022년 5월인 식품들의 식품 ID, 식품 이름, 총매출을 조회하는 SQL문을 작성해주세요. 이때 결과는 총매출을 기준으로 내림차순 정렬해주시고 총매출이 같다면 식품 ID를 기준으로 오름차순 정렬해주세요.

---

### 예시

`FOOD_PRODUCT` 테이블이 다음과 같고

| PRODUCT_ID | PRODUCT_NAME | PRODUCT_CD | CATEGORY | PRICE |
| --- | --- | --- | --- | --- |
| P0011 | 맛있는콩기름 | CD_OL00001 | 식용유 | 4880 |
| P0012 | 맛있는올리브유 | CD_OL00002 | 식용유 | 7200 |
| P0013 | 맛있는포도씨유 | CD_OL00003 | 식용유 | 5950 |
| P0014 | 맛있는마조유 | CD_OL00004 | 식용유 | 8950 |
| P0015 | 맛있는화조유 | CD_OL00005 | 식용유 | 8800 |
| P0016 | 맛있는참기름 | CD_OL00006 | 식용유 | 7100 |
| P0017 | 맛있는들기름 | CD_OL00007 | 식용유 | 7900 |
| P0018 | 맛있는고추기름 | CD_OL00008 | 식용유 | 6100 |
| P0019 | 맛있는카놀라유 | CD_OL00009 | 식용유 | 5100 |
| P0020 | 맛있는산초유 | CD_OL00010 | 식용유 | 6500 |

`FOOD_ORDER` 테이블이 다음과 같을 때

| ORDER_ID | PRODUCT_ID | AMOUNT | PRODUCE_DATE | IN_DATE | OUT_DATE | FACTORY_ID | WAREHOUSE_ID |
| --- | --- | --- | --- | --- | --- | --- | --- |
| OD00000056 | P0012 | 1000 | 2022-04-04 | 2022-04-21 | 2022-04-25 | FT19980002 | WH0032 |
| OD00000057 | P0014 | 2500 | 2022-04-14 | 2022-04-27 | 2022-05-01 | FT19980002 | WH0033 |
| OD00000058 | P0017 | 1200 | 2022-05-19 | 2022-05-28 | 2022-05-28 | FT20070002 | WH0033 |
| OD00000059 | P0017 | 1000 | 2022-05-24 | 2022-05-30 | 2022-05-30 | FT20070002 | WH0038 |
| OD00000060 | P0019 | 2000 | 2022-05-29 | 2022-06-08 | 2022-06-08 | FT20070002 | WH0035 |

SQL을 실행하면 다음과 같이 출력되어야 합니다.

| PRODUCT_ID | PRODUCT_NAME | TOTAL_SALES |
| --- | --- | --- |
| P0017 | 맛있는들기름 | 17380000 |
| P0019 | 맛있는카놀라유 | 10200000 |

- 답
    
    ```sql
    SELECT FP.PRODUCT_ID, FP.PRODUCT_NAME, SUM(FP.PRICE * FO.AMOUNT) AS TOTAL_SALES
    FROM FOOD_PRODUCT FP
    JOIN FOOD_ORDER FO ON FP.PRODUCT_ID = FO.PRODUCT_ID
    WHERE DATE_FORMAT(FO.PRODUCE_DATE,"%Y%m") = '202205'
    GROUP BY FP.PRODUCT_NAME
    ORDER BY TOTAL_SALES DESC, FP.PRODUCT_ID ASC
    ```