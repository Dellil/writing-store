# This
this는 챕터2의 실행컨텍스트가 활성화 될 때 생성된다.

즉 함수를 호출할 때 this가 결정된다. (결정된다라는 의미는 js에서의 this는 변화무쌍한 녀석이라 '한 가지'로 결정된다는 의미)

## 전역공간의 This

전역공간의 This는 전역 객체를 가리킨다.

**전역변수를 선언하면 자바스크립트 엔진은 이를 전역 객체의 프로퍼티로 할당함**

---

# 메서드 호출로서의 메서드 내부의 This

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

ㅋㅋ근데 구조분해 문법을 활용한 상태에서 호출을 하게되면 상위의 존재가 this가 된답니다!

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