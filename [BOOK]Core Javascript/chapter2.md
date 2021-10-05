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

### 호이스팅 예제

```jsx
function a() {
	console.log(b);    // (1)
	var b = 'bbb';     // 수집 대상 1(변수 선언)
	console.log(b);    // (2)
	function b() {}    // 수집 대상 2(함수 선언)
	console.log(b);    // (3)
}

a();
```

위와 같은 코드를 호이스팅 개념을 적용해 바꿔보자

```jsx
function a() {
    var b;
    var b = function b() {} // <-- 바뀐 부분
    
    console.log(b);     // (1)
    var b = 'bbb';
    console.log(b);     // (2)
    console.log(b);     // (3)
}

// console.log의 결과는 차례대로 함수, 'bbb', 'bbb'가 나온다.
```
변수는 선언부를 끌어 올리고, 함수 선언은 전체를 끌어올리기때문에 위 코드처럼 바뀌었다.

**짧게 짚고 넘어가기 - 함수 선언문과 함수 표현식**
1. 함수 선언문
    1. function 정의부만 존재하고 별도 할당 명령이 없음
2. 함수 표현식
    1. function을 별도의 변수에 할당하는걸 말함
    2. **함수명**이 있는 함수 표현식을 **기명 함수 표현식**, 아닌 것을 **익명 함수 표현식**이라 부른다.
    3. **보통** 함수 표현식이라 함은 **익명 함수 표현식**을 말한다.

```jsx
console.log(sum(1,2));
console.log(multiply(3,4));

// 함수 선언문
function sum(a, b) {
    return a + b;
}

// 함수 표현식
var multiply = function(a, b) {
    return a * b;
}
```
```jsx
// 호이스팅이 적용된 코드
var sum = function sum(a, b) {
	return a + b;
}
var multiply;
console.log(sum(1, 2));
console.log(multiply(3, 4));

multiply = function(a, b) { // 변수 할당부는 원래 자리에 남김
	return a * b;
}

// 결과: multiply is not a function Error
```
---

# Scope

- 스코프
    
    식별자의 유효범위
    
    es5 이전까지의 스코프는 오직 함수에 의해서만 생성됐다.
    

## Scope Chain

스코프를 안에서 바깥으로 검색해나가는 것

**LexicalEnvironment**의 **outerEnvironmentReference**가 가능케한다.

### How to work?

outerEnvironmentReference는 호출된 함수의 ***선언될 당시***의 **LexicalEnvironment**를 참조하기 때문이다.

따라서 **무조건 스코프 체인상에서 가장 먼저 발견된 식별자에만 접근 가능**하게 된다. *(outerEnvironmentReference는 연결 리스트 형태를 띄게 된다!)*

```jsx
var a = 1;
var outer = function() {
	var inner = function() {
		console.log(a);
		var a = 3;
	}
	inner();
	console.log(a);
}
outer();
console.log(a);
```

코드 실행 동작 과정

1. 전역컨텍스트가 활성화된다. 전역컨텍스트의 *environmentRecord*에는 a와 outer 식별자가 저장된다.
2. a에는 1, outer에는 함수를 할당한다.
3. outer함수를 호출하면서 outer실행컨텍스트가 활성화된다.
4. outer 실행컨텍스트의 *environmentRecord*에는 inner 식별자가 저장된다. outer의 *outerEnvironmentReference*에는 outer함수가 선언될 당시의 **전역 LexicalEnvironment가 담긴다.**
5. outer에 있는 변수 inner에게 함수를 할당한다.
6. inner를 호출한다.
7. inner 실행컨텍스트의 *environmentRecord*에는 a식별자가 저장된다. inner의 *outerEnvironmentReference*에는 **outer함수의 LexicalEnvironment가 담긴다.**
8. inner함수안에서는 `console.log(a)` 로 식별자 a에 접근하려하지만 a에는 아무 값이 할당되있지 않아 `undefined` 를 출력한 다음, a에게 3을 할당하게 되면서 inner함수 실행이 종료된다.
9. 다시 outer 실행컨텍스트가 활성화되면서 `console.log(a)` 로 a에 접근을 시도한다. outer실행컨텍스트의 *LexicalEnvironment*의 *environmentRecord*에는 a가 없으므로, outer의 *outerEnvironmentReference*에 있는 *environmentRecord*로 넘어가서 a를 찾는다. 이때 outer 실행컨텍스트의 *outerEnvironmentReference*는 전역 컨텍스트가 되고, 전역 컨텍스트의 *environmentRecord*에는 a가 있으므로 a의 값을 반환하게 된다. 그러므로 `1`이 출력된다.
10. outer 함수의 실행이 끝나고 전역 컨텍스트가 다시 활성화되면서 `console.log(a)` 를 실행하려 한다.
11. 전역 컨텍스트의 *environmentRecord* 에는 a가 있으므로 a의 값을 반환한다. 그러므로 `1` 이 출력된다.