# `validator.cpp` Skill

`validator.cpp`는 `problem.md`에 적힌 입력 형식과 제약을 검증하는 `testlib.h` 기반 validator입니다.

validator는 단순히 입력을 읽는 코드가 아닙니다. 잘못된 입력을 거절해야 합니다.

## 작성 순서

`validator.cpp`는 항상 `problem.md`를 먼저 읽은 뒤 작성합니다.

`problem.md`에 적힌 입력 형식, 제약 조건, 구조적 조건만을 기준으로 validator를 작성합니다. `problem.md`에 없는 규칙을 validator에서 임의로 추가하면 안 됩니다.

`problem.md`가 모호하거나 validator 작성에 필요한 정보가 빠져 있으면 `validator.cpp`를 작성하지 말고 먼저 사용자에게 질문합니다.

## Required Skeleton

```cpp
#include "testlib.h"

int main(int argc, char* argv[]) {
    registerValidation(argc, argv);

    // Read and validate input here.

    inf.readEof();
}
```

`registerValidation(argc, argv);`는 반드시 호출합니다.

## General Rules

line-based input이면 strict parsing을 기본으로 합니다.

숫자 리터럴이 4자리 이상이면 C++ digit separator인 `'`를 사용해 3자리마다 끊어 씁니다.

```cpp
int n = inf.readInt(3, 100'000'000, "n");
long long x = inf.readLong(1, 1'000'000'000'000LL, "x");
```

```cpp
int n = inf.readInt(1, 200'000, "n");
inf.readEoln();
```

한 줄에 여러 값이 있으면 공백과 줄 끝을 명확히 검증합니다.

```cpp
int n = inf.readInt(1, 200'000, "n");
inf.readSpace();
int m = inf.readInt(0, 200'000, "m");
inf.readEoln();
```

배열:

```cpp
for (int i = 0; i < n; i++) {
    if (i > 0) inf.readSpace();
    int a = inf.readInt(1, 1'000'000'000, format("a[%d]", i + 1));
}
inf.readEoln();
```

문자열:

```cpp
std::string s = inf.readToken("[a-z]+", "s");
inf.readEoln();
```

마지막에는 항상:

```cpp
inf.readEof();
```

## Ask Before Writing

다음 정보가 불명확하면 `validator.cpp`를 추측해서 만들지 말고 먼저 질문합니다.

Graph:

- directed인지 undirected인지
- vertex 번호가 `0..N-1`인지 `1..N`인지
- self-loop 허용 여부
- 중복 간선 허용 여부
- connected graph인지
- simple graph인지
- tree, forest, DAG, arbitrary graph 중 무엇인지
- weighted graph인지
- weight 범위가 무엇인지

Array:

- 값 범위
- 중복 허용 여부
- 정렬 조건
- permutation 여부
- 음수 허용 여부

String:

- 길이 범위
- 허용 문자
- 대문자 허용 여부
- 공백 포함 가능 여부

Grid:

- `H`, `W` 범위
- 허용 문자
- 시작점, 도착점 같은 특수 문자의 개수
- 테두리 조건

Multiple test cases:

- `T`가 있는지
- `T`의 범위
- 제약이 case별인지 전체 합 기준인지
- `sum N <= ...` 같은 전역 제약이 있는지

## Tree Validation

입력이 tree라면 validator는 반드시 다음을 확인합니다.

- `N` 범위
- 간선이 정확히 `N - 1`개인지
- 모든 endpoint가 범위 안인지
- self-loop가 없는지
- undirected duplicate edge가 없는지
- graph가 connected인지

`N - 1`개의 간선만으로 tree라고 판단하지 않습니다. connectivity도 확인해야 합니다.

DSU 또는 DFS/BFS를 사용합니다.

## Undirected Graph Validation

문제 명세에 따라 다음을 확인합니다.

- endpoint 범위
- self-loop 정책
- duplicate edge 정책
- connected 조건
- edge count 범위
- weight 범위

중복 검사를 할 때는 간선을 정규화합니다.

```text
(min(u, v), max(u, v))
```

## Directed Graph Validation

문제 명세에 따라 다음을 확인합니다.

- endpoint 범위
- self-loop 정책
- duplicate directed edge 정책
- DAG 조건
- reachability 또는 connectivity 조건
- edge count 범위
- weight 범위

DAG라면 topological sort 또는 DFS color로 cycle이 없는지 확인합니다.

## Permutation Validation

배열이 `1..N`의 permutation이면 다음을 확인합니다.

- 정수가 정확히 `N`개인지
- 모든 값이 `[1, N]`인지
- 각 값이 정확히 한 번씩 등장하는지

## Sorted Array Validation

정렬 조건이 있으면 명세에 맞게 확인합니다.

- Nondecreasing: `a[i - 1] <= a[i]`
- Strictly increasing: `a[i - 1] < a[i]`
- Nonincreasing: `a[i - 1] >= a[i]`
- Strictly decreasing: `a[i - 1] > a[i]`

## Grid Validation

grid 입력이면 다음을 확인합니다.

- `H`, `W` 범위
- row가 정확히 `H`개인지
- 각 row 길이가 `W`인지
- 각 문자가 허용된 문자 집합에 속하는지
- `S`, `G` 같은 특수 문자가 필요한 개수만큼 있는지
- 테두리 벽 같은 구조 조건이 있는지

예:

```cpp
std::string row = inf.readToken("[.#SG]+", format("grid[%d]", i + 1));
ensuref((int)row.size() == w, "grid row %d must have length %d", i + 1, w);
inf.readEoln();
```

## Consistency Rule

validator는 `problem.md`에 없는 규칙을 임의로 강제하면 안 됩니다.

validator에 필요한 규칙이 `problem.md`에 없다면 먼저 `problem.md`를 업데이트하거나 사용자에게 질문합니다.

## Final Check

`validator.cpp`를 작성하거나 수정한 뒤에는 최종적으로 컴파일 체크만 수행합니다.

컴파일은 레포지토리의 `header/testlib.h`를 기준으로 확인합니다.

예:

```bash
g++ -std=c++17 -O2 -Wall -Wextra -I header problems/{문제 제목}/validator.cpp -o /tmp/validator_check
```

이 단계의 목적은 `#include "testlib.h"`가 `header/testlib.h`로 해석되고, validator 코드가 컴파일되는지 확인하는 것입니다.

사용자가 명시적으로 요청하지 않았다면 validator 실행 테스트, 랜덤 테스트, generator 연동 테스트, 제출 코드 채점 테스트는 수행하지 않습니다.
