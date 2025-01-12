---
title: "[알고리즘] 희소 배열(Sparse Table) 알고리즘"
author: HashTable
categories: [알고리즘, 이론]
tags: [알고리즘, 희소 배열, 자료구조]
date: 2022-04-16 15:24:00 +0900
last_modified_at: 2022-04-16 16:00:00 +0900
img_path: /blog-assets/algorithm/sparse-table
image:
  path: title.svg
math: true
---

## 🤔 **희소 배열이 뭐야?**

희소 배열의 정의는 배열 원소의 갯수가 배열의 길이보다 작은 배열이라고 합니다.
그런데 제가 설명할 알고리즘에서는 배열의 원소 갯수가 배열의 길이와 같은데 어째서 희소 배열인지는 잘 모르겠네요...

이 알고리즘은 노드당 out-degree(나가는 간선의 갯수)가 1인 단방향 그래프에서,
**i 번 노드로부터 N 번 이동했을 때 어떤 노드에 도달하는지 알아내는데 매우 유용**합니다.

---

## 📃 **희소 배열 알고리즘**

### **그냥 구하면 안돼?**
N 개의 노드들에 대해 M 번 이동 후 위치를 묻는다고 해봅시다. 그냥 구하게된다면 이 때의 시간복잡도는
$O(N M)$일 것입니다. 만약 N 과 M 이 100,000이라면 N*M = 100억입니다. 그냥 구하기엔 너무 비효율적이죠.

---

### **알고리즘 설명**

알고리즘의 핵심은 **미리 $1, 2, 4, 8, ..., 2^n$ 번 이동했을 때의 위치를 미리 구하고 쿼리를 수행하는 것**입니다.

#### 기본적인 아이디어
![예시 그래프](figure1.svg)

위와 같은 그래프가 있다고 해봅시다. 모든 그래프의 out-degree 는 1이고 단방향 그래프입니다.
이 때 8번 노드로부터 7번 이동하는 경우를 생각해봅시다.

$$7 = 4 + 2 + 1$$

7번 이동 후 위치는 4번 이동, 2번 이동, 1번 이동 후의 위치와 같습니다.

> 1.&nbsp;8번 노드에서 4번 이동하면 2번 노드입니다.
>
> 2.&nbsp;2번 노드에서 2번 이동하면 4번 노드입니다.
>
> 3.&nbsp;4번 노드에서 1번 이동하면 5번 노드입니다.
{:.prompt-info}

7 번 이동 후 위치를 3번만에 알 수 있네요. 이 때문에 쿼리를 수행하기 전 미리 각 노드에서 $2^n$번 이동 후의 위치를 계산해 놓아야합니다.
N 개 노드의 M 번 후 위치를 알아야 한다면
* 전처리에 $O(N \log M)$ 만큼의 시간/공간을 소요하고
* 쿼리당 $O(log M)$ 의 시간을 소요합니다.

---

#### 전처리

![예시 그래프](figure2.svg)

| n($2^n$) \ 노드 |  1  |  2  |  3  |  4  |  5  |  6  |  7  |  8  |  9  | 10  | 11  |
|:-------------:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|   n = 0 (1)   |  2  |  3  |  4  |  5  |  1  |  5  |  6  |  6  |  4  | 11  | 10  |
|   n = 1 (2)   |  3  |  4  |  5  |  1  |  2  |  1  |  5  |  5  |  5  | 10  | 11  |
|   n = 2 (4)   |  5  |  1  |  2  |  3  |  4  |  3  |  2  |  2  |  2  | 10  | 11  |

M 번 이동 후 위치까지 알아야한다면, 위와 같은 배열/리스트를 n = 0 부터 n = $[\log_2 M]$ 까지 미리 만들어 두어야 합니다.
만드는 방법은 간단합니다. $f_n (x) = (x$번 노드에서 $2^n$번 움직인 후 위치$)$라고 하면

$$f_n (x) = f_{n-1} (f_{n-1} (x))$$

입니다. 예를들어 1번 노드에서 2번 이동, 9번 노드에서 4번 이동 후 위치를 생각해보겠습니다.

$$f_1 (1) = f_0 (f_0 (1)) = f_0 (2) = 3$$

$$f_2 (9) = f_1 (f_1 (9)) = f_1 (5) = 2$$

따라서 1번 노드에서 2번 이동 후 3번 노드, 9번 노드에서 4번 이동 후 2번 노드임을 알 수 있습니다.
이렇게 n = 0 부터 n = $[\log_2 M]$ 까지 만들면 됩니다.

---

#### 쿼리 수행

쿼리 수행도 어렵지 않습니다. 이동해야하는 횟수 M 을 2의 거듭제곱꼴인 수들의 합으로 나타내주면 됩니다.
위에서 7 = 4 + 2 + 1 로 쪼갰던 것처럼요. 어떻게 쪼갤 수 있을까요?

$$ 7 = 111_{(2)} = 4 + 2 + 1 $$

$$ 13 = 1101_{(2)} = 8 + 4 + 1 $$

$$ 21 = 10101_{(2)} = 16 + 4 + 1 $$

감이 오시나요? i 번째 비트가 켜져있다면 $2^i$ 번 이동해주어야 합니다. while 문을 돌면서 켜져있는 비트일 때마다
전처리 리스트를 이용해 구해나가면 됩니다.

---

## 💻 **코드**

1-based 그래프인 경우의 코드입니다.

* 노드 갯수: N
* 최대 이동 수: M
* arr[i]: i 번 노드의 1번 이동 후 위치


```python
from math import log2

log2_m = log2(M)
sparse_table = [[0] * (N + 1) for _ in range(log2_m + 1)]

def build_sparse_table():
    # 1번 이동 후
    sparse_table[0] = arr

    for i in range(1, log2_m + 1):
        for j in range(1, N + 1):
            sparse_table[i][j] = sparse_table[i-1][sparse_table[i-1][j]]

# x 노드의 m번 이동 후 위치
def query(m, x):
    bit = 0
    while (1 << bit) <= n:
        if n & (1 << bit):
            x = sparse_table[bit][x]
        bit += 1
    return x
```

---

## 참고

[capo님 블로그](https://hello-capo.netlify.app/algorithm-sparse-table/#references){:target="_blank"},
[남남서님 블로그](https://namnamseo.tistory.com/entry/Sparse-Table){:target="_blank"}
