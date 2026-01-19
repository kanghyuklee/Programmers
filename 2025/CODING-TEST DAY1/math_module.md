# math module

## 분수의 덧셈

### **문제 설명**

첫 번째 분수의 분자와 분모를 뜻하는 `numer1`, `denom1`, 두 번째 분수의 분자와 분모를 뜻하는 `numer2`, `denom2`가 매개변수로 주어집니다. 두 분수를 더한 값을 기약 분수로 나타냈을 때 분자와 분모를 순서대로 담은 배열을 return 하도록 solution 함수를 완성해보세요.

---

### 제한사항

- 0 <`numer1`, `denom1`, `numer2`, `denom2` < 1,000

---

### 입출력 예

| numer1 | denom1 | numer2 | denom2 | result |
| --- | --- | --- | --- | --- |
| 1 | 2 | 3 | 4 | [5, 4] |
| 9 | 2 | 1 | 3 | [29, 6] |

---

### 입출력 예 설명

입출력 예 #1

- 1 / 2 + 3 / 4 = 5 / 4입니다. 따라서 [5, 4]를 return 합니다.

입출력 예 #2

- 9 / 2 + 1 / 3 = 29 / 6입니다. 따라서 [29, 6]을 return 합니다.

- 답
    
    ```sql
    import math
    
    def solution(numer1, denom1, numer2, denom2):
        if denom1 == 0 or denom2 == 0:
            assert False
        else:
            a = (numer1 * denom2) + (numer2 * denom1)  # 분자
            b = denom1 * denom2  # 분모
            c = math.gcd(a, b)  # 최대공약수
            answer = [a // c, b // c] # 기약분수
    
        return answer
    ```
    

---

## 🔥 자주 쓰이는 `math` 모듈 꿀함수 리스트

### ✅ 기본 연산

| 함수 | 설명 | 예시 |
| --- | --- | --- |
| `math.gcd(a, b)` | 최대공약수 | `math.gcd(18, 12) → 6` |
| `math.lcm(a, b)` | 최소공배수 (3.9+) | `math.lcm(4, 6) → 12` |
| `math.floor(x)` | 내림 (정수로) | `math.floor(3.9) → 3` |
| `math.ceil(x)` | 올림 (정수로) | `math.ceil(3.1) → 4` |
| `math.trunc(x)` | 소수점 버림 | `math.trunc(3.8) → 3` |
| `math.isclose(a, b)` | 두 수가 거의 같으면 True | `math.isclose(0.1 + 0.2, 0.3)` |

---

### ✅ 제곱/제곱근/로그

| 함수 | 설명 | 예시 |
| --- | --- | --- |
| `math.pow(x, y)` | x의 y제곱 | `math.pow(2, 3) → 8.0` |
| `math.sqrt(x)` | 제곱근 | `math.sqrt(16) → 4.0` |
| `math.exp(x)` | e^x | `math.exp(1) → 2.718...` |
| `math.log(x)` | 자연로그 (ln) | `math.log(10)` |
| `math.log10(x)` | 상용로그 (log10) | `math.log10(100) → 2` |

---

### ✅ 삼각함수 (라디안 단위)

| 함수 | 설명 |
| --- | --- |
| `math.sin(x)` | 사인 |
| `math.cos(x)` | 코사인 |
| `math.tan(x)` | 탄젠트 |
| `math.radians(deg)` | 도 → 라디안 변환 |
| `math.degrees(rad)` | 라디안 → 도 변환 |

---

### ✅ 기타 꿀함수

| 함수 | 설명 | 예시 |
| --- | --- | --- |
| `math.factorial(n)` | n! (팩토리얼) | `math.factorial(5) → 120` |
| `math.comb(n, k)` | 조합 (nCk) | `math.comb(5, 2) → 10` |
| `math.perm(n, k)` | 순열 (nPk) | `math.perm(5, 2) → 20` |
| `math.copysign(x, y)` | x에 y의 부호 적용 | `math.copysign(3, -1) → -3.0` |
| `math.fabs(x)` | 절댓값 (float용) | `math.fabs(-5.5) → 5.5` |