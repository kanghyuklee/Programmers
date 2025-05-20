# 문제 1 - JOIN

## FrontEnd 개발자 찾기

### **문제 설명**

`SKILLCODES` 테이블은 개발자들이 사용하는 프로그래밍 언어에 대한 정보를 담은 테이블입니다. `SKILLCODES` 테이블의 구조는 다음과 같으며, `NAME`, `CATEGORY`, `CODE`는 각각 스킬의 이름, 스킬의 범주, 스킬의 코드를 의미합니다. 스킬의 코드는 2진수로 표현했을 때 각 bit로 구분될 수 있도록 2의 제곱수로 구성되어 있습니다.

| NAME | TYPE | UNIQUE | NULLABLE |
| --- | --- | --- | --- |
| NAME | VARCHAR(N) | Y | N |
| CATEGORY | VARCHAR(N) | N | N |
| CODE | INTEGER | Y | N |

`DEVELOPERS` 테이블은 개발자들의 프로그래밍 스킬 정보를 담은 테이블입니다. `DEVELOPERS` 테이블의 구조는 다음과 같으며, `ID`, `FIRST_NAME`, `LAST_NAME`, `EMAIL`, `SKILL_CODE`는 각각 개발자의 ID, 이름, 성, 이메일, 스킬 코드를 의미합니다. `SKILL_CODE` 컬럼은 INTEGER 타입이고, 2진수로 표현했을 때 각 bit는 `SKILLCODES` 테이블의 코드를 의미합니다.

| NAME | TYPE | UNIQUE | NULLABLE |
| --- | --- | --- | --- |
| ID | VARCHAR(N) | Y | N |
| FIRST_NAME | VARCHAR(N) | N | Y |
| LAST_NAME | VARCHAR(N) | N | Y |
| EMAIL | VARCHAR(N) | Y | N |
| SKILL_CODE | INTEGER | N | N |

예를 들어 어떤 개발자의 `SKILL_CODE`가 400 (=b'110010000')이라면, 이는 `SKILLCODES` 테이블에서 CODE가 256 (=b'100000000'), 128 (=b'10000000'), 16 (=b'10000') 에 해당하는 스킬을 가졌다는 것을 의미합니다.

---

### 문제

`DEVELOPERS` 테이블에서 Front End 스킬을 가진 개발자의 정보를 조회하려 합니다. 조건에 맞는 개발자의 ID, 이메일, 이름, 성을 조회하는 SQL 문을 작성해 주세요.

결과는 ID를 기준으로 오름차순 정렬해 주세요.

---

### 예시

예를 들어 `SKILLCODES` 테이블이 다음과 같고,

| NAME | CATEGORY | CODE |
| --- | --- | --- |
| C++ | Back End | 4 |
| JavaScript | Front End | 16 |
| Java | Back End | 128 |
| Python | Back End | 256 |
| C# | Back End | 1024 |
| React | Front End | 2048 |
| Vue | Front End | 8192 |
| Node.js | Back End | 16384 |

`DEVELOPERS` 테이블이 다음과 같다면

| ID | FIRST_NAME | LAST_NAME | EMAIL | SKILL_CODE |
| --- | --- | --- | --- | --- |
| D165 | Jerami | Edwards | `jerami_edwards@grepp.co` | 400 |
| D161 | Carsen | Garza | `carsen_garza@grepp.co` | 2048 |
| D164 | Kelly | Grant | `kelly_grant@grepp.co` | 1024 |
| D163 | Luka | Cory | `luka_cory@grepp.co` | 16384 |
| D162 | Cade | Cunningham | `cade_cunningham@grepp.co` | 8452 |

다음과 같이 `DEVELOPERS` 테이블에 포함된 개발자 중 Front End 스킬을 가진을 가진 개발자의 정보가 결과에 나와야 합니다.

| ID | EMAIL | FIRST_NAME | LAST_NAME |
| --- | --- | --- | --- |
| D161 | `carsen_garza@grepp.co` | Carsen | Garza |
| D162 | `cade_cunningham@grepp.co` | Cade | Cunningham |
| D165 | `jerami_edwards@grepp.co` | Jerami | Edwards |
- 답
    
    ```sql
    select distinct dev.id, dev.email, dev.first_name, dev.last_name
    from developers dev
    join skillcodes scode 
    on scode.code & dev.skill_code = scode.code
    where scode.category = 'Front End'
    order by dev.id
    ```
    

---

이 쿼리는 딱 보니까 **비트마스킹으로 스킬 매칭**하는 고급 기술 썼네!

하나하나 뜯어줄게 — 이건 진짜 실무에서도 자주 쓰이는 개념이야🔥

---

## 🎯 쿼리 요약

```sql
sql
복사편집
SELECT DISTINCT A.ID, A.EMAIL, A.FIRST_NAME, A.LAST_NAME
FROM DEVELOPERS AS A
  INNER JOIN SKILLCODES AS B
    ON (A.SKILL_CODE & B.CODE = B.CODE)
WHERE B.CATEGORY = 'Front End'
ORDER BY A.ID ASC;

```

---

## 🧠 테이블 역할

| 테이블 | 설명 |
| --- | --- |
| `DEVELOPERS` | 각 개발자 정보 (`SKILL_CODE` 컬럼에 **비트로 된 스킬들** 저장되어 있음) |
| `SKILLCODES` | 각 스킬의 고유 비트값 (`CODE`)과 `CATEGORY` 존재함 |

---

## 🔍 핵심 로직 해석

### 📌 이 조건:

```sql
sql
복사편집
A.SKILL_CODE & B.CODE = B.CODE

```

는 비트 연산자 `&`를 사용한 건데,

이 뜻은:

> “개발자(A)가 가지고 있는 스킬(SKILL_CODE)이,
> 
> 
> B.CODE(특정 스킬)을 **포함하고 있다면** 매치된다.”
> 

### 예시:

- `A.SKILL_CODE = 6` → 이진수 `110`
- `B.CODE = 2` → 이진수 `010`

```
text
복사편집
110 (6)
& 010 (2)
= 010 → 2

```

👉 결과가 B.CODE랑 같으면 해당 스킬 포함 O

---

## ✅ WHERE 조건:

```sql
sql
복사편집
WHERE B.CATEGORY = 'Front End'

```

→ 프론트엔드 관련 스킬만 매칭함.

---

## ✅ DISTINCT 사용한 이유:

개발자가 **여러 Front End 스킬**을 갖고 있을 수도 있기 때문에,

JOIN으로 여러 row가 나올 수 있음 → 그래서 중복 제거하려고 `DISTINCT` 사용! ✔️

---

## 🔚 최종 해석 요약:

> “Front End 관련 스킬을 하나 이상 보유한 개발자들만 추출하고, 중복 없이 정렬하라!”
> 
> 
> → 실무에서 **비트 플래그** 방식으로 스킬 세트 저장할 때 자주 사용하는 패턴이야!
>