# `generator.cpp` 작성 규칙

`generator.cpp`는 문제 입력 데이터를 생성하는 `testlib.h` 기반 generator입니다.

사용자가 명시적으로 요청한 경우에만 작성합니다.

## 목적

generator는 표준 출력으로 하나의 올바른 입력 파일을 출력합니다.

출력된 내용은 해당 문제의 `problem.md` 입력 형식과 `validator.cpp` 검증 규칙을 통과해야 합니다.

## 필수 구조

반드시 `testlib.h`를 사용하고 `registerGen(argc, argv, 1);`를 호출합니다.

```cpp
#include "testlib.h"
#include <bits/stdc++.h>
using namespace std;

int main(int argc, char* argv[]) {
    registerGen(argc, argv, 1);

    // Generate and print input here.

    return 0;
}
```

## 출력 방식

기본 출력은 `cout`을 사용합니다.

`cout`으로 문제 입력 형식 그대로 출력하면 generator output이 하나의 테스트 입력이 됩니다.

예:

```cpp
cout << n << '\n';
for (int i = 0; i < n; i++) {
    cout << a[i];
    if (i != n-1) cout << ' ';
}
cout << '\n';
```

## 줄 끝 공백 금지

각 줄은 반드시 trim된 상태여야 합니다.

금지:

```cpp
for (int i = 0; i < n; i++) {
    cout << a[i] << ' ';
}
cout << '\n';
```

위 코드는 줄 끝에 공백이 생기므로 사용하지 않습니다.

허용:

```cpp
for (int i = 0; i < n; i++) {
    cout << a[i];
    if (i != n-1) cout << ' ';
}
cout << '\n';
```

## 개행 규칙

- 모든 줄은 `\n`으로 끝냅니다.
- 줄 끝의 `\n` 직전에 공백 문자, 탭 문자가 있으면 안 됩니다.
- 마지막 줄도 입력 형식상 필요한 경우 `\n`으로 끝냅니다.
- 불필요한 빈 줄을 출력하지 않습니다.

## Harness Concept

generator는 자유롭게 출력하는 코드처럼 보이지만, 실제로는 엄격한 출력 하네스 규칙을 따라야 합니다.

항상 아래 순서로 생각합니다.

1. `problem.md`의 입력 형식을 확인한다.
2. `validator.cpp`가 요구하는 범위와 구조적 조건을 확인한다.
3. 그 조건을 만족하는 데이터를 만든다.
4. 각 줄을 trailing whitespace 없이 출력한다.
5. 생성 결과가 validator를 통과해야 한다고 가정하고 작성한다.

## Random Generation

난수는 testlib의 `rnd`를 사용합니다.

예:

```cpp
int n = rnd.next(1, 100);
int x = rnd.next(0, 1000000000);
```

커맨드라인 인자를 사용하는 경우에도 `registerGen` 이후에 처리합니다.

## Generator Arguments

`generator.cpp`는 `gen_script.txt`에서 넘기는 인자로 생성 목적을 바꿀 수 있게 작성합니다.

예:

```cpp
int mode = opt<int>("mode", 0);
int n = opt<int>("n", 100);
```

인자를 통해 다음 용도를 쉽게 만들 수 있어야 합니다.

- 작은 `n`으로 brute force 또는 정답 검증용 입력 생성
- 큰 `n`으로 최대 크기 테스트 생성
- 최소값, 최대값, 경계값 테스트 생성
- 특정 구조만 만드는 스트레스 테스트 생성

인자 설계는 `problem.md`의 제약과 `validator.cpp`의 검증 조건을 벗어나면 안 됩니다.

## Multiple Generators

generator가 하나면 파일 이름은 `generator.cpp`를 사용합니다.

용도별 generator가 여러 개 필요하면 파일 이름을 반드시 아래 형식으로 작성합니다.

```text
generator-{number}.cpp
```

예:

```text
generator-1.cpp
generator-2.cpp
generator-3.cpp
```

여러 generator를 둘 때는 역할을 명확히 나눕니다.

- `generator-1.cpp`: 작은 랜덤 입력, brute force 검증용
- `generator-2.cpp`: 큰 랜덤 입력, 성능 테스트용
- `generator-3.cpp`: 최소/최대/경계값 테스트용

`generator-small.cpp`, `gen.cpp`, `random.cpp` 같은 이름은 사용하지 않습니다.

## Structural Data

tree, graph, permutation, grid 같은 구조를 생성할 때는 `problem.md`와 `validator.cpp`의 조건을 모두 만족해야 합니다.

예:

- tree라면 connected하고 cycle이 없어야 합니다.
- simple graph라면 self-loop와 duplicate edge가 없어야 합니다.
- permutation이라면 모든 값이 정확히 한 번씩 등장해야 합니다.
- grid라면 허용된 문자만 출력하고, 필요한 특수 문자의 개수를 맞춰야 합니다.

## 금지 사항

- `registerGen(argc, argv, 1);` 없이 작성하지 않습니다.
- 줄 끝 공백을 출력하지 않습니다.
- `problem.md`에 없는 형식을 출력하지 않습니다.
- validator가 거절할 수 있는 데이터를 생성하지 않습니다.
- 디버그 로그를 `stdout`에 출력하지 않습니다.
- 여러 generator가 필요한데 `generator-{number}.cpp` 형식을 벗어난 이름을 사용하지 않습니다.
