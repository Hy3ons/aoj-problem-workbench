# `gen_script.txt` 작성 규칙

`gen_script.txt`는 generator를 어떤 순서와 인자로 실행해 테스트 입력을 만들지 적는 스크립트 파일입니다.

사용자가 명시적으로 요청한 경우에만 작성합니다.

## 목적

`gen_script.txt`는 `generator.cpp` 또는 `generator-{number}.cpp`에 다양한 인자를 넘겨 다음 테스트를 쉽게 만들기 위한 파일입니다.

- 작은 입력으로 brute force 또는 정답 검증
- 큰 입력으로 성능 테스트
- 최소값, 최대값, 경계값 테스트
- 특정 구조를 반복 생성하는 스트레스 테스트
- 직접 작성한 manual test 포함

## 기본 문법

스펙 §6 문법을 사용합니다.

허용하는 기본 형태:

```text
manual
generator-name [arguments]
for i in 1..5:
    generator-name [arguments]
```

예:

```text
manual
generator 1
for i in 1..5:
    generator i
```

## Generator Name

generator가 하나면 이름은 `generator`를 사용합니다.

```text
generator 1
for i in 1..10:
    generator i
```

여러 generator가 있으면 C++ 파일 이름과 맞춰 아래 이름을 사용합니다.

```text
generator-1
generator-2
generator-3
```

예:

```text
generator-1 1
generator-2 1
for i in 1..20:
    generator-3 i
```

여러 generator의 C++ 파일 이름은 반드시 다음 형식이어야 합니다.

```text
generator-{number}.cpp
```

## Arguments

`gen_script.txt`의 인자는 generator가 읽어 생성 방식을 바꾸는 값입니다.

generator는 이 인자를 사용해 다음을 조절할 수 있어야 합니다.

- seed 또는 case 번호
- `n`의 크기
- 랜덤/극값/특수 구조 mode
- graph density
- tree shape
- grid pattern

인자는 간단하고 재현 가능해야 합니다.

예:

```text
manual
generator 1 small
generator 2 max
for i in 1..30:
    generator i random
```

## Required Test Mix

가능하면 아래 성격의 테스트가 포함되도록 작성합니다.

```text
manual
generator 1 min
generator 2 max
for i in 1..10:
    generator i small
for i in 11..30:
    generator i random
for i in 31..40:
    generator i stress
```

각 항목의 의미:

- `manual`: 사람이 직접 만든 예제 또는 특수 케이스
- `small`: 작은 `n`, brute force 검증에 적합한 입력
- `max`: 최대 크기 또는 성능 한계 입력
- `random`: 일반 랜덤 입력
- `stress`: 특정 약점을 노리는 반복 테스트

## Consistency Rule

`gen_script.txt`는 존재하는 generator만 호출해야 합니다.

`generator.cpp` 하나만 있으면 `generator`만 호출합니다.

`generator-1.cpp`, `generator-2.cpp`처럼 여러 generator가 있으면 `generator-1`, `generator-2`처럼 번호가 붙은 이름만 호출합니다.

`gen_script.txt`에서 넘기는 인자는 generator 코드가 실제로 해석할 수 있어야 합니다.

## 금지 사항

- 존재하지 않는 generator 이름을 호출하지 않습니다.
- 여러 generator가 필요한데 `generator-{number}.cpp` 형식을 벗어난 이름을 사용하지 않습니다.
- generator가 해석하지 못하는 인자를 넘기지 않습니다.
- `problem.md`와 `validator.cpp`를 통과하지 못하는 테스트 생성을 의도하지 않습니다.

