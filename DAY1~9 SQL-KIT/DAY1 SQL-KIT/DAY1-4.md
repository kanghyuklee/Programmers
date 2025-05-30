# 문제 4 - JOIN

## 상품 별 오프라인 매출 구하기

### **문제 설명**

다음은 어느 의류 쇼핑몰에서 판매중인 상품들의 상품 정보를 담은 `PRODUCT` 테이블과 오프라인 상품 판매 정보를 담은 `OFFLINE_SALE` 테이블 입니다. `PRODUCT` 테이블은 아래와 같은 구조로 `PRODUCT_ID`, `PRODUCT_CODE`, `PRICE`는 각각 상품 ID, 상품코드, 판매가를 나타냅니다.

| Column name | Type | Nullable |
| --- | --- | --- |
| PRODUCT_ID | INTEGER | FALSE |
| PRODUCT_CODE | VARCHAR(8) | FALSE |
| PRICE | INTEGER | FALSE |

상품 별로 중복되지 않는 8자리 상품코드 값을 가지며, 앞 2자리는 카테고리 코드를 의미합니다.

`OFFLINE_SALE` 테이블은 아래와 같은 구조로 되어있으며 `OFFLINE_SALE_ID`, `PRODUCT_ID`, `SALES_AMOUNT`, `SALES_DATE`는 각각 오프라인 상품 판매 ID, 상품 ID, 판매량, 판매일을 나타냅니다.

| Column name | Type | Nullable |
| --- | --- | --- |
| OFFLINE_SALE_ID | INTEGER | FALSE |
| PRODUCT_ID | INTEGER | FALSE |
| SALES_AMOUNT | INTEGER | FALSE |
| SALES_DATE | DATE | FALSE |

동일한 날짜, 상품 ID 조합에 대해서는 하나의 판매 데이터만 존재합니다.

---

### 문제

`PRODUCT` 테이블과 `OFFLINE_SALE` 테이블에서 상품코드 별 매출액(판매가 * 판매량) 합계를 출력하는 SQL문을 작성해주세요. 결과는 매출액을 기준으로 내림차순 정렬해주시고 매출액이 같다면 상품코드를 기준으로 오름차순 정렬해주세요.

---

### 예시

예를 들어 `PRODUCT` 테이블이 다음과 같고

| PRODUCT_ID | PRODUCT_CODE | PRICE |
| --- | --- | --- |
| 1 | A1000011 | 15000 |
| 2 | A1000045 | 8000 |
| 3 | C3000002 | 42000 |

`OFFLINE_SALE` 테이블이 다음과 같다면

| OFFLINE_SALE_ID | PRODUCT_ID | SALES_AMOUNT | SALES_DATE |
| --- | --- | --- | --- |
| 1 | 1 | 2 | 2022-02-21 |
| 2 | 1 | 2 | 2022-03-02 |
| 3 | 3 | 3 | 2022-05-01 |
| 4 | 2 | 1 | 2022-05-24 |
| 5 | 1 | 2 | 2022-07-14 |
| 6 | 2 | 1 | 2022-09-22 |

각 상품 별 총 판매량과 판매가는 다음과 같습니다.

- `PRODUCT_CODE` 가 `A1000011`인 상품은 총 판매량이 6개, 판매가가 15,000원
- `PRODUCT_CODE` 가 `A1000045`인 상품은 총 판매량이 2개, 판매가가 8,000원
- `PRODUCT_CODE` 가 `C3000002`인 상품은 총 판매량이 3개, 판매가가 42,000원

그러므로 각 상품 별 매출액을 계산하고 정렬하면 결과가 다음과 같이 나와야 합니다.

| PRODUCT_CODE | SALES |
| --- | --- |
| C3000002 | 126000 |
| A1000011 | 90000 |
| A1000045 | 16000 |

- 답
    
    ```sql
    select 
    distinct pr.PRODUCT_CODE, sum(os.sales_amount) * pr.price as 'SALES' 
    from OFFLINE_SALE os
    right join PRODUCT pr on os.product_id = pr.product_id
    group by os.product_id
    order by SALES desc, pr.PRODUCT_CODE asc
    ```

- 해석

⚠️ 성능적으로 아쉬운 부분들:
1. DISTINCT + GROUP BY 조합
→ GROUP BY가 이미 중복 제거 역할을 하니까 DISTINCT는 불필요해. 오히려 오버헤드만 줘.

2. GROUP BY os.product_id인데 SELECT에는 pr.PRODUCT_CODE만 있음
→ GROUP BY 키와 SELECT 컬럼이 정확히 매칭되지 않으면 의도치 않은 결과가 나오거나 성능이 떨어질 수 있어.

3. 계산(sum * price)이 GROUP BY 안에서 일어남
→ price는 PRODUCT 테이블의 고정값일 가능성이 크니까, 그걸 sum 밖에서 곱하는 게 낫지.

4. RIGHT JOIN?
→ PRODUCT가 중심 테이블이면 LEFT JOIN이 더 직관적이고 최적화하기 쉬워.

- 개선

    ```sql
    SELECT 
    pr.PRODUCT_CODE,
    COALESCE(SUM(os.sales_amount), 0) * pr.price AS SALES
    FROM 
        PRODUCT pr
    LEFT JOIN 
        OFFLINE_SALE os ON os.product_id = pr.product_id
    GROUP BY 
        pr.product_id, pr.PRODUCT_CODE, pr.price
    ORDER BY 
        SALES DESC, pr.PRODUCT_CODE ASC;
    ```