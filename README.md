# AOJ Problem Workbench

AOJ 스타일 프로그래밍 문제를 AI와 함께 작성하기 위한 문서 중심 작업 공간입니다.
문제 지문, `testlib.h` 기반 validator, generator, 의도적으로 틀린 WA 풀이를 일관된 규칙으로 관리합니다.

GitHub 저장소 설명으로는 아래 문장을 권장합니다.

```
AI-assisted workspace for drafting AOJ-style programming problems, validators, generators, and wrong-answer solutions.
```

## 구조

기본적으로 문제 하나는 아래 디렉터리에 세 파일로 관리합니다.

```
problems/{문제 제목}/README.md
problems/{문제 제목}/problem.md
problems/{문제 제목}/validator.cpp
```

- `README.md`: 문제 폴더의 현재 상태, 파일 역할, 명세 요약, 남은 확인 사항을 기록합니다.
- `problem.md`: AOJ 스타일 한국어 문제 지문입니다.
- `validator.cpp`: `problem.md`의 입력 형식과 제약을 검증하는 `testlib.h` 기반 validator입니다.

사용자가 명시적으로 요청한 경우에만 아래 파일을 추가합니다.

```
problems/{문제 제목}/generator.cpp
problems/{문제 제목}/generator-{number}.cpp
problems/{문제 제목}/gen_script.txt
problems/{문제 제목}/wa-sol-{number}.cpp
problems/{문제 제목}/wa-sol-{number}.py
problems/{문제 제목}/wa-sol-{number}.java
```

## 작성 규칙

AI 에이전트는 먼저 `AGENTS.md`를 읽고 작업합니다.
문제 생성 시에는 `skills/problem.md`와 `skills/validator.md`를 추가로 읽어야 합니다.

기본 흐름은 다음과 같습니다.

1. `problems/{문제 제목}/` 폴더를 만듭니다.
2. `README.md`를 만들고 현재 상태와 파일 역할을 기록합니다.
3. `problem.md`를 먼저 작성합니다.
4. 입력 형식과 제약이 충분히 명확해진 뒤에만 `validator.cpp`를 작성합니다.
5. `validator.cpp` 작성 후 `header/testlib.h` 기준 컴파일 체크만 수행합니다.
6. 파일을 만들거나 수정할 때마다 문제별 `README.md`를 함께 갱신합니다.

`generator.cpp`, `gen_script.txt`, `wa-sol-{number}` 파일은 사용자가 명시적으로 요청한 경우에만 만듭니다.
