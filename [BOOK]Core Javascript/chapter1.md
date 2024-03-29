# 목표

- **기본형과 참조형 타입이 서로 다르게 동작하는 이유**를 알 수 있다.
- 데이터 타입을 활용할 수 있다.

---

우선 자바스크립트의 데이터 타입은 **기본형**과 **참조형**으로 나뉜다.

### 기본형

- number
- boolean
- string
- symbol
- undefined
- null

### 참조형

- object
    - Map
    - WeakMap
    - Set
    - WeakSet
    - Array
    - Function
    - Date
    - RegExp

기본형과 참조형을 구분하는 기준은 무엇일까?

⇒ 대개 기본형은 할당, 연산시 복제한다 하고, 참조는 참조시 복제한다고들 한다.

하지만, **둘 모두 복제**를 한다. 다만 ***그 복제 대상***이 다를 뿐이다.

기본형은 ***값이 담긴 주소값***을 복제한다.

참조형은 ***값이 담긴 주소값의 묶음을 갖고 있는 주소값***을 복제한다.

---

기본형 특) 불변성임

변수 → 변할 수 있는 데이터, 식별자 → 변수명

**JS 메모리 영역**

변수 영역과 값 영역으로 나뉨 (책 저자가 이해를 위해 지어낸 것)

변수 영역 ⇒ 변수명과 해당 변수의 데이터 값의 주솟값 저장

값 영역 ⇒ 데이터 저장

영역 분리 이유 ⇒ 메모리 관리 효율적으로 할려고, 데이터 변환할 때 이미 선언되어 있는 주소를 건드리게 될 수 있어서

ex) 'abc' 값을 만들고 나서 'abcdef'를 만들려 하면 'abc'의 주소값 참조를 안 하고, 그냥 'abcdef'를 만든다.

효율적인 이유?

⇒ 숫자 5인 변수가 100개 있다고 할 때, 영역 분리가 안 되어 있으면 숫자 크기 * 100만큼의 메모리 소모가 일어나게 되는데, 영역 분리가 되어있는 경우 숫자 하나 크기 만큼만 메모리 소모가 일어나기 때문에 영역 분리는 효율적임

---

# 정리

1. JS의 데이터 타입은 기본형, 참조형이 있다.
    - 기본형은 숫자, boolean, 문자열, null, undefined, symbol이 있다.
    - 참조형은 object가 있고, object의 하위 타입으로 배열, 함수, Date, 정규표현식, Map(WeakMap), Set(WeakSet)이 있다.
    - 기본형과 참조형을 구분하는 방법은?
        - 기본형: 값이 있는 주솟값을 복제
        - 참조형: 값이 담긴 주솟값들로 이루어진 '묶음'을 가르키는 주솟값을 복제
2. **기본형에 대해 좀 더 자세한 정보**
    - 기본형은 불변성을 띈다.
        - JS에서 변수 선언과 할당을 할 때, 변수명이 저장된 곳이 있고, 변수의 값이 저장된 곳이 있다.
        - 새로운 값이 생길 때마다, 변수의 값을 저장하는 곳에서 그 값을 저장한다. (문자열 'abc'를 만들고 'abcdef'를 만들 때 기존의 'abc'를 이용하지 않는다는 뜻)
        - 이렇게 영역 구분으로 얻는 이점은 메모리 관리 효율 상승을 얻을 수 있다.
        - 결론적으로 값이 변할 수가 **'없어서'** 불변성을 띈다고 할 수 있다.
        - 불변성 여부: 한번 만든 값을 바꿀 수 있는지의 여부
3. 변수와 상수
    - 변수에 값 할 당이 끝났는데 또 다시 값 할당을 할 수 있는지의 여부
    (변수에 값 주소 넣었는데 또 다시 다른 값 주소 넣을 수 있냐구!)
    - 변수, 상수는 불변성과 관련 X
4. **참조형에 대해 좀 더 자세한 정보**
    - 기본적으로 가변 값이지만 `Object.defineProperty`, `Object.freeze` 으로 변경 못 하게 할 수 있다. 또한 참조형을 불변값으로 활용하기도 한다.
    - 참조형(object)이 가변인 이유 ⇒ 해당 객체의 프로퍼티 수정이 가능하기 때문이다. (프로퍼티도 변수 취급이라서 그렇다.)
    - 가변이 성립하는 조건
        - 참조형 데이터 그 자체가 변경되는 경우가 아닌, 그 내부 프로퍼티만 바뀔 때 성립된다.
        - `let a = { b: 1}` ⇒ a에는 객체의 주솟값, 객체는 b의 주솟값을 갖고 있음.
    - *객체*는 **가변**(내부 프로퍼티 변경시)인데 **불변 객체**로 어떻게 만듦?
        - 원본 객체의 의미?
            
            원본 객체의 프로퍼티를 수정하지 않고, 새 객체를 만들어 해당 새 객체의 프로퍼티를 변경한다는 의미임. 이로써, 객체끼리의 주솟값도 틀리고 프로퍼티끼리의 주솟값도 틀릴 수 있음
            
        - 내부 프로퍼티를 변경할 때마다 **새 객체**를 만들어 재할당하면 됨
        
        ```jsx
        var user = {
        	name: 'Chanhee',
        	gender: 'male',
        };
        
        var changeName = function(user, newName) {
        	return {
        		name: newName,
        		gender: user.gender,
        	};
        };
        
        var user2 = changeName(user, 'Jang');
        
        if(user !== user2) {
        	console.log('유저 정보가 변경되었습니다.') // 유저 정보가 변경되었습니다.
        }
        ```
        
        - 불변 객체가 필요한 상황: 값으로 전달 받은 객체를 수정해도 원본은 바뀌면 안되는 상황일 때.
        - Shallow Copy(얕은 복사)와 Deep Copy(깊은 복사)를 통해 불변 객체를 만들 수 있다.
            - Shallow Copy: 바로 아래 단계 값(프로퍼티)만 복사
            - Deep Copy: 객체 내부 모든 값을 전부 복사
        - 간단히 Deep Copy하는 방법
            - JSON으로 바꿨다가 object로 parsing하기
            (function, __proto__, getter/setter) 같은 건 무시한다.
            - VO를 다룰 때 좋다.
5. undefined와 null
    - JS엔진이 자동으로 부여하는 경우
        - 값을 대입하지 않은 변수에 access할 때
        - 객체에 없는 프로퍼티에 접근할 때
        - return문이 없거나 함수 실행 결과가 호출되지 않았을 때
    - 배열이 empty면 순회대상에서 제외된다.
        - 배열도 객체기때문, 존재하지 않는 프로퍼티에 접근 못한다.
    - var은 LE가 활성화되면서 undefined로 초기화된다.
    - let, const는 LE가 활성화될 때 생겨나지만, 특정값을 할당하기 전까지 변수 접근이 불가능하다.
    - null은 type of 걸면 object가 나온다.