# 문제 2 - SELECT

## 어린 동물 찾기

### **문제 설명**

`ANIMAL_INS` 테이블은 동물 보호소에 들어온 동물의 정보를 담은 테이블입니다. `ANIMAL_INS` 테이블 구조는 다음과 같으며, `ANIMAL_ID`, `ANIMAL_TYPE`, `DATETIME`, `INTAKE_CONDITION`, `NAME`, `SEX_UPON_INTAKE`는 각각 동물의 아이디, 생물 종, 보호 시작일, 보호 시작 시 상태, 이름, 성별 및 중성화 여부를 나타냅니다.

| NAME | TYPE | NULLABLE |
| --- | --- | --- |
| ANIMAL_ID | VARCHAR(N) | FALSE |
| ANIMAL_TYPE | VARCHAR(N) | FALSE |
| DATETIME | DATETIME | FALSE |
| INTAKE_CONDITION | VARCHAR(N) | FALSE |
| NAME | VARCHAR(N) | TRUE |
| SEX_UPON_INTAKE | VARCHAR(N) | FALSE |

동물 보호소에 들어온 동물 중 젊은 동물[1](https://school.programmers.co.kr/learn/courses/30/lessons/59037#fn1)의 아이디와 이름을 조회하는 SQL 문을 작성해주세요. 이때 결과는 아이디 순으로 조회해주세요.

### 예시

예를 들어 `ANIMAL_INS` 테이블이 다음과 같다면

| ANIMAL_ID | ANIMAL_TYPE | DATETIME | INTAKE_CONDITION | NAME | SEX_UPON_INTAKE |
| --- | --- | --- | --- | --- | --- |
| A365172 | Dog | 2014-08-26 12:53:00 | Normal | Diablo | Neutered Male |
| A367012 | Dog | 2015-09-16 09:06:00 | Sick | Miller | Neutered Male |
| A365302 | Dog | 2017-01-08 16:34:00 | Aged | Minnie | Spayed Female |
| A381217 | Dog | 2017-07-08 09:41:00 | Sick | Cherokee | Neutered Male |

이 중 젊은 동물은 Diablo, Miller, Cherokee입니다. 따라서 SQL문을 실행하면 다음과 같이 나와야 합니다.

| ANIMAL_ID | NAME |
| --- | --- |
| A365172 | Diablo |
| A367012 | Miller |
| A381217 | Cherokee |

---

본 문제는 [Kaggle의 "Austin Animal Center Shelter Intakes and Outcomes"](https://www.kaggle.com/aaronschlegel/austin-animal-center-shelter-intakes-and-outcomes)에서 제공하는 데이터를 사용하였으며 [ODbL](https://opendatacommons.org/licenses/odbl/1.0/)의 적용을 받습니다.

---

1. `INTAKE_CONDITION`이 Aged가 아닌 경우를 뜻함 [↩](https://school.programmers.co.kr/learn/courses/30/lessons/59037#fnref1)

- 답
    
    ```sql
    SELECT ANIMAL_ID, NAME 
    FROM ANIMAL_INS 
    WHERE INTAKE_CONDITION != 'Aged' 
    ORDER BY ANIMAL_ID
    ```