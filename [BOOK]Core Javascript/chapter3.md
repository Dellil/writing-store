# This
this는 챕터2의 실행컨텍스트가 활성화(*JS엔진이 실행컨텍스트를 사용하려고 마음을 먹었을 때*) 될 때 생성된다.

즉 함수를 호출할 때 this가 결정된다. (결정된다라는 의미는 js에서의 this는 변화무쌍한 녀석이라 '한 가지'로 결정된다는 의미)

## 전역공간의 This

전역공간의 This는 전역 객체를 가리킨다.

**전역변수를 선언하면 자바스크립트 엔진은 이를 전역 객체의 프로퍼티로 할당함**

---

# 메서드 호출로서의 그 메서드 내부의 This

메서드 호출로 this를 부르게 되면, 그 this는 해당 메서드의 객체가 된다.

즉...

```jsx
var func = function() {
	console.log(this);
}

var obj = {
	method: func
}

obj.method(); // { method: f } 출력됨
```

이렇게 된다.

ㅋㅋ근데 구조분해 문법을 활용한 상태에서 호출을 하게되면 상위의 존재가 this가 된다.

```jsx
var func = function() {
	console.log(this);
}

var obj = {
	method: func
}
obj.method(); // { method: f } 출력됨

const { method } = obj;
method(); // Window {0: Window, 1: Window, window: Window ...} 출력됨
```

이건 메서드로서의 호출(함수 이름앞에 객체가 명시된 호출 ex) *obj.method*)이 아닌, 함수로서의 호출이기때문에 이런 결과가 나타나게 된 것이다.

---


# 함수 호출로서의 그 함수 내부의 this

예제를 보자

```jsx
var obj1 = {
	outer: function () {
		console.log(this); // (1)
		var innerFunc = function() {
			console.log(this); // (2), (3) - (3)이 있는 이유는 
												 // obj2.innerMethod도 결국 innerFunc를 사용함
		}
		innerFunc();
		
		var obj2 = {
			innerMethod: innerFunc
		};
		obj2.innerMethod();
	}
obj1.outer();
```

위의 예제 결과를 예측해보자.

답은 (1): obj1, (2): 전역객체, (3): obj2이다.

코드 실행 동작 과정

1. 1번째줄 - 객체 생성함, outer 프로퍼티가 있고 익명함수가 연결됨. 이렇게 만들어진 객체를 obj1에 할당함
2. 15번째 줄 - obj.outer() 호출
3. 2번째 줄 - obj1.outer() 함수의 실행컨텍스트가 생성, 스코프 체인 정보를 수집하고, this를 바인딩함. 이 때 outer앞에 점이 찍혀있었으므로 **메서드로서 호출**을 한것 obj1이 this 바인딩 된다.
4. 4번째 줄 - 호이스팅된 변수 innerFunc는 outer 스코프 내에서만 접근할 수 있는 지역변수. 이 지역변수에 익명 함수 할당
5. 8번째 줄 - innerFunc 호출
6. 4번째줄 - 실행컨텍스트 생성됨, 함수로서 호출된 것이므로 this 바인딩 안됨 따라서 스코프 체인상의 최상위 객체인 전역 객체가 바인딩됨
7. 13번째 줄 - obj2.innerMethod를 호출함. innerMethod앞에 점이 있었으므로 **메서드로서의 호출**임. 따라서 obj2가 바인딩됨

---

# 메서드의 내부 함수에서의 This 바인딩 우회

```jsx
var obj1 = {
	outer: function () {
		console.log(this);
		var innerFunc = function() {
			console.log(this); 
		}
		innerFunc();
		
		var self = this;
		var innerFunc2 = function() {
			console.log(self);
		}
		innerFunc2();
	}
}
obj1.outer();
```

변수에 this를 할당한다. obj1.outer()로 *(메서드로서의)*호출했기때문에 해당 컨텍스트의 this는 outer 그 자체가 된다. 따라서 그 this를 self에 할당하고 그것을 내부 함수에서 사용해주는 식이다.

```jsx
var obj = {
	outer: function() {
		var innerFunc = () => {
			console.log(this);
		}
		innerFunc();
	}
};
obj.outer();
```

ES6에 추가된 화살표 함수를 사용하게 되면 실행컨텍스트를 생성할 때 this 바인딩 과정이 빠지게 된다. 따라서 innerFunc의 실행컨텍스트에는 this바인딩이 빠지게 되어 해당 환경내의 this는 outer(상위스코프)의 this가 된다. 즉 outer를 가리키게 된다.

---

# 콜백 함수에서의 this

딱히 정해지지 않았다. 일단 기본적으로는 전역객체를 참조한다.

`.addEventListener` 와 점을 붙여서 쓰는 경우, 해당 점의 앞쪽(`document.querySelector().addEventListener()`)이 this가 된다.

---

# 생성자 함수 내부에서의 this

생성자 함수로 호출 되면, this는 자기 자신(인스턴스)이 된다.