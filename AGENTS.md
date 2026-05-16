# Agent Context

이 레포지토리는 AOJ 스타일 문제를 AI와 함께 작성하기 위한 문서 중심 레포입니다.

기본 작업은 문제 하나당 아래 세 파일을 만드는 것입니다.

```text
problems/{문제 제목}/README.md
problems/{문제 제목}/problem.md
problems/{문제 제목}/validator.cpp
```

사용자가 명시적으로 요청한 경우에만 아래 파일을 추가로 만듭니다.

```text
problems/{문제 제목}/generator.cpp
problems/{문제 제목}/generator-{number}.cpp
problems/{문제 제목}/generator-{purpose}.cpp
problems/{문제 제목}/gen_script.txt
problems/{문제 제목}/wa-sol-{number}.cpp
problems/{문제 제목}/wa-sol-{number}.py
problems/{문제 제목}/wa-sol-{number}.java
```

문제 폴더 이름은 영어일 필요가 없습니다. 사용자가 정한 문제 제목을 폴더 이름으로 사용합니다.

## 진입 규칙

사용자가 `{문제 제목} 문제 생성해줘`, `{문제 제목} 만들어줘`처럼 짧게 요청해도 문제 생성 요청으로 처리합니다.

문제 생성 요청을 받으면 이 `AGENTS.md`만 읽고 멈추지 말고, 실제 작성 전에 반드시 아래 문서를 추가로 읽습니다.

- `skills/problem.md`
- `skills/validator.md`

사용자가 제목이나 주제만 준 경우에는 에이전트가 구체적인 입력 형식, 제약, 예제, 출력 조건을 설계할 수 있습니다. 단, 사용자가 준 요구사항과 충돌하거나 의도를 확정할 수 없는 핵심 조건이 있으면 작성 전에 질문합니다.

사용자가 generator 또는 gen_script를 명시적으로 요청한 경우에만 아래 문서를 추가로 읽습니다.

- `skills/generator.md`
- `skills/gen-script.md`

## 기본 행동

문제 생성을 요청받으면:

1. `problems/{문제 제목}/` 폴더를 만든다.
2. `README.md`를 만들고 문제 폴더의 현재 상태와 파일 역할을 기록한다.
3. 먼저 `skills/problem.md`의 서식에 맞춰 `problem.md`를 작성한다.
4. `problem.md`를 작성하는 중 사용자가 준 요구사항 안에서 입력 형식, 제약, 그래프 조건, 예제, 출력 조건 등 모호한 점이 있으면 반드시 사용자에게 질문한다. 단, 사용자가 제목이나 주제만 준 경우에는 진입 규칙에 따라 에이전트가 구체 명세를 설계할 수 있다.
5. `problem.md`를 만들거나 수정한 뒤 `README.md`에 역할, 작성 이유, 현재 명세 상태를 갱신한다.
6. `problem.md`의 입력 형식과 제약이 충분히 명확해진 뒤에만 `validator.cpp`를 작성한다.
7. `validator.cpp`는 `problem.md`를 기준 문서로 삼아 `skills/validator.md`의 지침에 맞춰 작성한다.
8. `validator.cpp`를 만들거나 수정한 뒤 `README.md`에 검증 대상, 검증 범위, 의존하는 명세를 갱신한다.
9. `problem.md`와 `validator.cpp`가 같은 입력 형식과 제약을 말하는지 확인한다.

## 문제별 `README.md`

모든 문제 폴더에는 반드시 `README.md`를 둔다.

`problems/{문제 제목}/README.md`는 해당 문제의 관리 요약 파일이다. 파일을 만들거나 수정할 때마다 함께 갱신한다.

반드시 기록할 내용:

- 문제의 현재 작성 상태
- 각 파일의 역할
- 각 파일을 만든 이유
- `problem.md`가 정의하는 입력, 출력, 제약의 요약
- `validator.cpp`가 검증하는 형식, 범위, 구조적 조건의 요약
- `generator.cpp`, `generator-{number}.cpp`, `generator-{purpose}.cpp`가 있다면 각 generator의 목적, 인자, 생성하는 테스트 성격
- `gen_script.txt`가 있다면 호출하는 generator, 인자 패턴, correctness/boundary/overflow/tle/small/max/random/stress/manual 테스트 구분
- `wa-sol-{number}.cpp`, `wa-sol-{number}.py`, `wa-sol-{number}.java`가 있다면 각 코드가 의도적으로 틀린 풀이인 이유, 틀린 로직의 위치, 실패하는 입력 유형
- 아직 모호하거나 사용자 확인이 필요한 항목

파일별 요약은 짧아도 되지만, 나중에 context 없이 읽는 AI가 현재 상황을 이어받을 수 있어야 한다.

## 추가 파일 작업

`generator.cpp` 생성을 요청받으면:

1. 먼저 `problem.md`와 `validator.cpp`를 확인한다.
2. `skills/generator.md`의 지침을 따른다.
3. `testlib.h`를 사용하고 `registerGen(argc, argv, 1);`를 호출한다.
4. 기본 출력은 `cout`을 사용한다.
5. 각 줄 끝에 공백이나 탭이 생기지 않게 출력한다.
6. 배열이나 리스트 출력은 `if (i) cout << ' ';` 패턴을 사용한다.
7. 디버그 로그를 `stdout`에 출력하지 않는다.
8. 생성된 입력이 `validator.cpp`를 통과하도록 작성한다.
9. 단순 랜덤 generator 하나로 끝내지 말고, 가능하면 목적별 generator를 나눈다.
10. solution 정당성 검토용, 극값/overflow 취약 풀이 검사용, 시간초과 취약 풀이 검사용 대형 테스트 generator를 우선 설계한다.
11. generator 파일을 만들거나 수정한 즉시 `README.md`에 generator의 목적, 인자, 생성하는 테스트 유형을 갱신한다.

여러 generator가 필요하면 새 파일 이름은 목적 기반으로 쓰는 것을 우선한다.

```text
generator-{purpose}.cpp
```

예: `generator-correctness.cpp`, `generator-boundary.cpp`, `generator-tle.cpp`, `generator-stress.cpp`

기존 문제에 이미 `generator-{number}.cpp`가 있으면 유지할 수 있지만, 새 generator는 목적이 드러나는 이름을 사용한다.

`gen_script.txt` 생성을 요청받으면:

1. `skills/gen-script.md`의 지침을 따른다.
2. 스펙 §6 문법을 사용한다.
3. `generator-name [arguments]`, `for i in 1..5:` 형식을 사용한다.
4. `manual`은 사용자가 수동 테스트를 명시적으로 요청한 경우에만 쓴다.
5. generator 인자를 바꿔 correctness, boundary, overflow, tle, small, max, random, stress 테스트를 쉽게 만들 수 있게 작성한다.
6. 존재하는 목적별 generator를 모두 호출하도록 구성한다.
7. 존재하지 않는 generator 이름이나 generator가 해석하지 못하는 인자를 쓰지 않는다.
8. `gen_script.txt`를 만들거나 수정한 즉시 `README.md`에 스크립트가 실행하는 generator와 테스트 구성을 갱신한다.

`wa-sol-{number}` 생성을 요청받으면:

1. 먼저 `problem.md`를 확인하고, 필요하면 `validator.cpp`도 확인한다.
2. 파일 이름은 반드시 `wa-sol-{number}.cpp`, `wa-sol-{number}.py`, `wa-sol-{number}.java` 중 하나를 사용한다.
3. 사용자가 언어를 지정하지 않으면 기본적으로 C++ 파일인 `wa-sol-{number}.cpp`를 만든다.
4. 이 파일은 정답 코드가 아니라, 문제를 해결하려는 코드 중 일부 로직이 의도적으로 틀린 코드여야 한다.
5. 단순 컴파일 에러, 런타임 에러 유발 코드, 입출력 형식 위반 코드가 아니라, 그럴듯하지만 특정 경우에 오답을 내는 코드로 작성한다.
6. 코드 안에는 틀린 로직이 있는 부분에 주석을 달아 왜 잘못된 접근인지 설명한다.
7. 가능하면 어떤 유형의 반례에서 실패하는지도 코드 주석에 짧게 적는다.
8. `README.md`에는 해당 `wa-sol-{number}` 파일의 목적, 의도적으로 틀린 알고리즘/조건, 코드상 주석 위치, 실패하는 입력 유형을 상세하게 갱신한다.
9. 여러 WA 코드가 필요하면 번호를 증가시켜 `wa-sol-1.cpp`, `wa-sol-2.cpp`처럼 만든다.

## 중요한 원칙

- 문제 설명은 이전 대화 맥락 없이도 이해 가능해야 한다.
- 각 문제 폴더의 `README.md`는 현재 상황을 이어받기 위한 관리 요약이므로, 문제 관련 파일을 수정할 때마다 반드시 함께 갱신한다.
- 항상 `problem.md`를 먼저 작성하고, 그 내용을 보고 `validator.cpp`를 작성한다.
- `validator.cpp`를 먼저 작성하거나, 아직 모호한 `problem.md`를 기준으로 validator를 추측해서 만들면 안 된다.
- validator는 `testlib.h`를 사용하고 `registerValidation(argc, argv);`를 호출해야 한다.
- validator의 C++ 숫자 리터럴이 4자리 이상이면 `100'000'000`처럼 `'`를 사용해 3자리마다 끊어 쓴다.
- 숫자 범위뿐 아니라 입력의 구조적 정당성도 검증해야 한다.
- tree, graph, grid, permutation, sorted array 같은 구조가 있으면 해당 구조가 정말 맞는지 validator에서 확인해야 한다.
- graph나 tree 규칙이 불명확하면 directed 여부, self-loop 허용 여부, 중복 간선 허용 여부, 연결성 조건 등을 사용자에게 물어본다.
- 코드 풀이, 제너레이터, 테스트 파일은 사용자가 명시적으로 요청하지 않으면 만들지 않는다.
- 사용자가 WA 풀이 생성을 명시적으로 요청한 경우에만 `wa-sol-{number}` 파일을 만든다.
- `wa-sol-{number}`는 정답 코드가 아니며, 의도적으로 틀린 로직과 그 이유를 코드 주석 및 `README.md`에 모두 기록해야 한다.
- `generator.cpp`를 요청받으면 `skills/generator.md`를 따른다.
- `gen_script.txt`를 요청받으면 `skills/gen-script.md`를 따른다.
- `gen_script.txt`는 generator 인자 설계와 함께 생각한다.
