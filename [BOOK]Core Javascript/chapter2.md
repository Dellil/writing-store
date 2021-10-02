# 실행 컨텍스트 (Execution Context)

- 실행할 코드의 환경 정보를 모아 놓은 객체
- 동일 환경에 놓인 코드를 실행할 때 필요한 환경 정보를 모아 컨텍스트를 구성하고 콜스택에 올려진다.
- 콜스택의 가장 위에 쌓인 컨텍스트와 관련 코드를 실행시켜서 전체 코드의 환경과 순서를 보장한다.

---

## 실행 컨텍스트를 구성하는 방법

1. 전역 환경
2. eval 사용
3. 함수(실행)

---

## 실행 컨텍스트 객체에 저장되는 것들은?

1. Variable Environment
    1. 현재 컨텍스트 내 식별자들의 정보 + 외부 환경 정보를 담음
    *'선언시점의 Lexical Environment'*의 스냅샷으로, 변경 사항은 반영이 안된다.
2. Lexical Environment
    1. Variable Environment와 같지만 변경사항이 반영된다.
    여기서 변경사항은 컨텍스트 내 식별자들의 정보와 외부 환경 정보에 관한 변경사항을 뜻함
3. ThisBinding
    1. this가 바라봐야 될 곳

실행컨텍스트 생성시 Variable Environment에 정보를 먼저 담고, 이를 복사해 Lexical Environment를 만든다. 그 이후 Lexical Environment를 주로 사용한다.

Variable Environment와 Lexical Environment의 내부는 **environment Record**와 **outer Environment**로 구성되어있다.

---

# Lexical Environment

- environment Record
    - 현재 컨텍스트와 관련된 코드의 식별자 정보를 수집한다.
    - 식별자 정보에 해당되는 것들은?
        - 컨텍스트를 구성하는 함수의 매개변수 식별자
        - 컨텍스트내부에 함수가 선언된 경우, 함수 그 자체
        - var로 선언된 변수 식별자
    - 컨텍스트 내부를 처음부터 끝까지 쭉 훑어나가며 **'순서대로'** 수집한다.

---

# Hoisting

- 실행컨텍스트의 동작방식(변수 정보 수집과정)에 대한 멘탈모델로써 호이스팅이라는 가상의 개념이 등장함
- JS 엔진이 실제로 끌어올리지는 않고 끌어 올린걸로 '간주하자'는 의미
    - 왜? ⇒ 코드가 실행되기 전인데 JS 엔진은 해당환경에 속한 코드의 변수명을 다 알고 있음(Lexical Environment의 environment Record에 수집됨)
    - 이미 다 알고 있다면 JS엔진은 식별자를 최상단으로 끌어올린담에 코드를 실행한다! 라고 생각해도 상관없기 때문