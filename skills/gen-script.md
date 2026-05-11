# `gen_script.txt` 작성 규칙

`gen_script.txt`는 generator를 어떤 순서와 인자로 실행해 테스트 입력을 만들지 적는 스크립트 파일입니다.

사용자가 명시적으로 요청한 경우에만 작성합니다.

## 목적

`gen_script.txt`는 `generator.cpp` 또는 `generator-{purpose}.cpp`에 다양한 인자를 넘겨 다음 테스트를 쉽게 만들기 위한 파일입니다.

- 작은 입력으로 brute force 또는 정답 검증
- solution 정당성 검토용 테스트
- 최소값, 최대값, 경계값, overflow 취약 풀이 검사용 테스트
- 시간초과를 유발할 수 있는 제한 내 대형 테스트
- 특정 구조를 반복 생성하는 스트레스 테스트
- 사용자가 명시적으로 요청한 경우에만 직접 작성한 manual test 포함

## 기본 문법

스펙 §6 문법을 사용합니다.

허용하는 기본 형태:

```text
generator-name [arguments]
for i in 1..5:
    generator-name [arguments]
```

예:

```text
generator 1
for i in 1..5:
    generator i
```

`manual`은 기본적으로 쓰지 않습니다. 사용자가 수동 테스트를 명시적으로 요청한 경우에만 필요한 위치에 추가합니다.

## Generator Name

generator가 하나면 이름은 `generator`를 사용합니다.

```text
generator 1
for i in 1..10:
    generator i
```

여러 generator가 있으면 C++ 파일 이름과 맞춰 아래 이름을 사용합니다.

```text
generator-correctness
generator-boundary
generator-tle
generator-stress
```

예:

```text
generator-correctness 1 small
generator-boundary 1 max
generator-tle 1 max
for i in 1..20:
    generator-stress i
```

여러 generator의 C++ 파일 이름은 새로 만들 때 다음 형식을 우선합니다.

```text
generator-{purpose}.cpp
```

기존 문제에 이미 `generator-{number}.cpp`가 있으면 그 이름에 맞춰 호출할 수 있지만, 새 generator는 목적 기반 이름을 사용합니다.

## Arguments

`gen_script.txt`의 인자는 generator가 읽어 생성 방식을 바꾸는 값입니다.

generator는 이 인자를 사용해 다음을 조절할 수 있어야 합니다.

- seed 또는 case 번호
- `n`의 크기
- 정당성 검토/극값/overflow/시간초과/특수 구조 mode
- graph density
- tree shape
- grid pattern

인자는 간단하고 재현 가능해야 합니다.

예:

```text
generator-correctness 1 small
generator-boundary 2 overflow
for i in 1..30:
    generator-stress i random
```

## Required Test Mix

가능하면 아래 성격의 테스트가 포함되도록 작성합니다.

```text
generator-correctness 1 small
generator-boundary 1 min
generator-boundary 2 max
generator-boundary 3 overflow
generator-tle 1 max
for i in 1..10:
    generator-correctness i random
for i in 11..30:
    generator-stress i random
for i in 31..40:
    generator-tle i max
```

각 항목의 의미:

- `correctness` 또는 `small`: 작은 `n`, brute force 검증과 solution 정당성 검토에 적합한 입력
- `boundary`, `min`, `max`, `overflow`: 최솟값, 최댓값, 경계값, overflow 취약 풀이를 노리는 입력
- `tle` 또는 `max`: 제한 내 큰 입력으로 시간초과 취약 풀이를 노리는 입력
- `random`: 일반 랜덤 입력
- `stress`: 특정 약점을 노리는 반복 테스트
- `manual`: 사용자가 명시적으로 요청한 경우에만 쓰는 사람이 직접 만든 예제 또는 특수 케이스

`gen_script.txt`는 존재하는 목적별 generator를 빠뜨리지 않고 호출해야 합니다. `generator-correctness.cpp`, `generator-boundary.cpp`, `generator-tle.cpp`, `generator-stress.cpp`가 있다면 스크립트 안에서 모두 사용합니다.

## Consistency Rule

`gen_script.txt`는 존재하는 generator만 호출해야 합니다.

`generator.cpp` 하나만 있으면 `generator`만 호출합니다.

`generator-{purpose}.cpp`처럼 여러 generator가 있으면 `generator-{purpose}` 이름으로 호출합니다.

`gen_script.txt`에서 넘기는 인자는 generator 코드가 실제로 해석할 수 있어야 합니다.

## 금지 사항

- 존재하지 않는 generator 이름을 호출하지 않습니다.
- 여러 generator가 필요한데 목적이 불분명한 이름을 사용하지 않습니다.
- generator가 해석하지 못하는 인자를 넘기지 않습니다.
- 존재하는 목적별 generator 일부만 호출하고 나머지를 빠뜨리지 않습니다.
- `problem.md`와 `validator.cpp`를 통과하지 못하는 테스트 생성을 의도하지 않습니다.
