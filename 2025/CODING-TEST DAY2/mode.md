# 최빈값 구하기

### **문제 설명**

최빈값은 주어진 값 중에서 가장 자주 나오는 값을 의미합니다. 정수 배열 `array`가 매개변수로 주어질 때, 최빈값을 return 하도록 solution 함수를 완성해보세요. 최빈값이 여러 개면 -1을 return 합니다.

---

### 제한사항

- 0 < `array`의 길이 < 100
- 0 ≤ `array`의 원소 < 1000

---

### 입출력 예

| array | result |
| --- | --- |
| [1, 2, 3, 3, 3, 4] | 3 |
| [1, 1, 2, 2] | -1 |
| [1] | 1 |

---

### 입출력 예 설명

입출력 예 #1

- [1, 2, 3, 3, 3, 4]에서 1은 1개 2는 1개 3은 3개 4는 1개로 최빈값은 3입니다.

입출력 예 #2

- [1, 1, 2, 2]에서 1은 2개 2는 2개로 최빈값이 1, 2입니다. 최빈값이 여러 개이므로 -1을 return 합니다.

입출력 예 #3

- [1]에는 1만 있으므로 최빈값은 1입니다.

---

※ 공지 - 2022년 10월 17일 제한 사항 및 테스트케이스가 수정되었습니다.

- 답
    
    ```sql
    def solution(array):
        a = len(array)
        if a < 100 and a > 0:
            cnt = [0] * 1001
            for i in array:
                cnt[i] += 1
    
            mode = 0
            for j in cnt:
                if j == max(cnt):
                    mode += 1
    
            if mode > 1:
                answer = -1
            else:
                answer = cnt.index(max(cnt))
        else:
            assert False
        
        return answer
    ```
    

---

### ✅ 1. `collections.Counter` 사용 (제일 많이 씀)

```python
from collections import Counter

data = [1, 2, 2, 3, 3, 3, 4]
counter = Counter(data)
mode = counter.most_common(1)[0][0]  # [(값, 빈도)] 중 가장 많이 나온 값
print(f"최빈값: {mode}")
```

결과:

```
최빈값: 3
```

---

### ✅ 2. `statistics.mode()` 사용

```python
import statistics

data = [1, 2, 2, 3, 3, 3, 4]
mode = statistics.mode(data)
print(f"최빈값: {mode}")
```

⚠️ 단점: **여러 개의 최빈값이 있을 경우 오류 발생할 수 있음** (동점 처리 안 함)

---

### ✅ 3. 수작업으로 구현 (카운트 리스트 활용)

```python
data = [1, 2, 2, 3, 3, 3, 4]
cnt = [0] * (max(data) + 1)

for num in data:
    cnt[num] += 1

mode = cnt.index(max(cnt))
print(f"최빈값: {mode}")
```