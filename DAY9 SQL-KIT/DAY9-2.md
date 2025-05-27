# 문제 2 - JOIN

## 그룹별 조건에 맞는 식당 목록 출력하기

### **문제 설명**

다음은 고객의 정보를 담은 `MEMBER_PROFILE`테이블과 식당의 리뷰 정보를 담은 `REST_REVIEW` 테이블입니다. `MEMBER_PROFILE` 테이블은 다음과 같으며 `MEMBER_ID`, `MEMBER_NAME`, `TLNO`, `GENDER`, `DATE_OF_BIRTH`는 회원 ID, 회원 이름, 회원 연락처, 성별, 생년월일을 의미합니다.

| Column name | Type | Nullable |
| --- | --- | --- |
| MEMBER_ID | VARCHAR(100) | FALSE |
| MEMBER_NAME | VARCHAR(50) | FALSE |
| TLNO | VARCHAR(50) | TRUE |
| GENDER | VARCHAR(1) | TRUE |
| DATE_OF_BIRTH | DATE | TRUE |

`REST_REVIEW` 테이블은 다음과 같으며 `REVIEW_ID`, `REST_ID`, `MEMBER_ID`, `REVIEW_SCORE`, `REVIEW_TEXT`,`REVIEW_DATE`는 각각 리뷰 ID, 식당 ID, 회원 ID, 점수, 리뷰 텍스트, 리뷰 작성일을 의미합니다.

| Column name | Type | Nullable |
| --- | --- | --- |
| REVIEW_ID | VARCHAR(10) | FALSE |
| REST_ID | VARCHAR(10) | TRUE |
| MEMBER_ID | VARCHAR(100) | TRUE |
| REVIEW_SCORE | NUMBER | TRUE |
| REVIEW_TEXT | VARCHAR(1000) | TRUE |
| REVIEW_DATE | DATE | TRUE |

---

### 문제

`MEMBER_PROFILE`와 `REST_REVIEW` 테이블에서 리뷰를 가장 많이 작성한 회원의 리뷰들을 조회하는 SQL문을 작성해주세요. 회원 이름, 리뷰 텍스트, 리뷰 작성일이 출력되도록 작성해주시고, 결과는 리뷰 작성일을 기준으로 오름차순, 리뷰 작성일이 같다면 리뷰 텍스트를 기준으로 오름차순 정렬해주세요.

---

### 예시

`MEMBER_PROFILE` 테이블이 다음과 같고

| MEMBER_ID | MEMBER_NAME | TLNO | GENDER | DATE_OF_BIRTH |
| --- | --- | --- | --- | --- |
| `jiho92@naver.com` | 이지호 | 01076432111 | W | 1992-02-12 |
| `jiyoon22@hotmail.com` | 김지윤 | 01032324117 | W | 1992-02-22 |
| `jihoon93@hanmail.net` | 김지훈 | 01023258688 | M | 1993-02-23 |
| `seoyeons@naver.com` | 박서연 | 01076482209 | W | 1993-03-16 |
| `yelin1130@gmail.com` | 조예린 | 01017626711 | W | 1990-11-30 |

`REST_REVIEW` 테이블이 다음과 같을 때

| REVIEW_ID | REST_ID | MEMBER_ID | REVIEW_SCORE | REVIEW_TEXT | REVIEW_DATE |
| --- | --- | --- | --- | --- | --- |
| R000000065 | 00028 | `soobin97@naver.com` | 5 | 부찌 국물에서 샤브샤브 맛이나고 깔끔 | 2022-04-12 |
| R000000066 | 00039 | `yelin1130@gmail.com` | 5 | 김치찌개 최곱니다. | 2022-02-12 |
| R000000067 | 00028 | `yelin1130@gmail.com` | 5 | 햄이 많아서 좋아요 | 2022-02-22 |
| R000000068 | 00035 | `ksyi0316@gmail.com` | 5 | 숙성회가 끝내줍니다. | 2022-02-15 |
| R000000069 | 00035 | `yoonsy95@naver.com` | 4 | 비린내가 전혀없어요. | 2022-04-16 |

SQL을 실행하면 다음과 같이 출력되어야 합니다.

| MEMBER_NAME | REVIEW_TEXT | REVIEW_DATE |
| --- | --- | --- |
| 조예린 | 김치찌개 최곱니다. | 2022-02-12 |
| 조예린 | 햄이 많아서 좋아요 | 2022-02-22 |

---

### 주의사항

`REVIEW_DATE`의 데이트 포맷이 예시와 동일해야 정답처리 됩니다.

- 답
    
    ```sql
    SELECT MP.MEMBER_NAME, RR.REVIEW_TEXT, DATE_FORMAT(RR.REVIEW_DATE,'%Y-%m-%d') AS REVIEW_DATE
    FROM MEMBER_PROFILE MP
    JOIN REST_REVIEW RR ON MP.MEMBER_ID = RR.MEMBER_ID
    WHERE RR.MEMBER_ID = (
        SELECT MEMBER_ID
        FROM REST_REVIEW
        GROUP BY MEMBER_ID
        ORDER BY COUNT(*) DESC
        LIMIT 1
    )
    ORDER BY RR.REVIEW_DATE ASC, RR.REVIEW_TEXT AS
    ```
    

## 🔍 단계별 해석

### 📌 FROM & JOIN

```sql
FROM MEMBER_PROFILE MP
JOIN REST_REVIEW RR ON MP.MEMBER_ID = RR.MEMBER_ID
```

- 회원 정보(`MEMBER_PROFILE`)와 리뷰 정보(`REST_REVIEW`)를 **회원 ID 기준으로 JOIN**

---

### 📌 WHERE 절 — "가장 리뷰 많이 쓴 회원"

```sql
WHERE RR.MEMBER_ID = (
    SELECT MEMBER_ID
    FROM REST_REVIEW
    GROUP BY MEMBER_ID
    ORDER BY COUNT(*) DESC
    LIMIT 1

```

- `REST_REVIEW`에서 `MEMBER_ID`로 그룹화
- `COUNT(*)`로 리뷰 개수를 세고
- 가장 많이 쓴 1명만 `LIMIT 1`로 뽑음 → 이 사람만 리뷰 대상이 됨

---

### 📌 SELECT 컬럼

```sql
SELECT
  MP.MEMBER_NAME,                     -- 회원 이름
  RR.REVIEW_TEXT,                    -- 리뷰 내용
  DATE_FORMAT(RR.REVIEW_DATE,'%Y-%m-%d') AS REVIEW_DATE  -- 리뷰 작성일 (YYYY-MM-DD 포맷)
```

---

### 📌 ORDER BY

```sql
ORDER BY RR.REVIEW_DATE ASC, RR.REVIEW_TEXT ASC;
```

- **날짜 오름차순** → 같은 날이면
- **리뷰 텍스트 알파벳순**으로 정렬