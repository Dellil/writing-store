> 이번엔 페이지별로 정리 되어 있음

### P.117
우리는 흔히 발생하는 상속 남용을 되짚어 볼거고, 이번시간에 당신은 객체 형태로 Class를 런타임에 장식하는 방법을 배우게 될 것입니다. 왜? 일단 장식(decorate)하는 기술을 알게 되면, 당신은 당신의 (또는 다른 사람의) 객체에 대한 새로운 책임을 *기본 클래스*를 변경하지 않고도 할 수 있기 때문이죠.

> 이번 시나리오는 커피숍이다.

### P.118
Starbuzz Coffee에 어서오세요. Starbuzz 커피는 가장 빠르게 성장하는 커피숍으로 이름을 날렸습니다. 어느정도냐면 동네 모퉁이에서 보면 다른 하나가 보일 정도죠
너무 빨리 커버렸음. 그들은 음료제공에 맞춰 주문시스템을 업데이트 하기위해 분주하게 움직이고 있음.

처음 사업을 시작했을때 이런 모양의 클래스를 디자인 함.
![사업구상한걸대충클래스다이어그램으로나타낸짤](https://lh3.googleusercontent.com/rwM_AsPQvwhdlh_NS3bCKWTLI9oPnw2uTOLKoJdtAbhLi2JnqGSzFM7-cKGnNR8x0Ev3qMa5nVjZ92Zx1gr13rmBmJpVP1kBNR7E8H2_5QauhuSKIOyW4Pu--zb4h0xzvaciD1cf)

Beverage는 커피샵에서 제공되는 모든 음료로 분류된 추상 클래스임.
description 변수는 각 하위 클래스들이 설정하며, "가장 뛰어난 다크로스트"와 같은 음료에 대한 설명을 포함하고 있음
그리고 각각의 서브 클래스들이 cost()를 구현해 음료의 가격을 리턴하고 있음

### P.119
커피외에도 스팀밀크, 두유, 모카 (초콜릿이라고도 알려진) 와 같은 토핑을 요구할 수 있고 휘핑밀크로도 덮어버릴 수도 있죠.
스타버즈는 제품 각각에 대해 요금을 부과하기 때문에 주문시스템에 꼭 집어넣어야 됩니다.
보면 이렇게 되어있음![돌겠네](https://lh4.googleusercontent.com/pAm1EQAcVc0DiUSEy4O7UBGWBg2kPhGPGxIQ9TkEIBIfilidE6QX9yfn60OWI0N0slHZZzvecxi-ysJ8DD28-1Gk7EHvuaJf6AWny11lyzlmQSq5viL2wbuEhYPgbrhGzgVvzQov)

각 비용 계산 방법은 다른 토핑과 함께 커피 비용을 계산함

### P.120
> Brain Power 페이지 ( 객관식 오지선다 )

*Starbuzz가 스스로 유지관리의 악몽을 꾸고 있다는 것은 꽤 명백해보입니다.*
우유의 값이 오르면 어떻게 될까요?
그리고 새로운 카라멜 토핑을 첨가할 때 무엇을 하는 것일까요?
**유지보수 문제를 넘어서, 지금 객체지향적인 설계 원칙중 무엇을 어기고 있나요?**

---

정말 멍청한 짓이야. 모든 클래스들은 필요없는데 말이야. 우린 그저 슈퍼클래스의 인스턴스 변수와 상속을 사용하여 토핑을 추적하면 되지 않을까?

ㅇㅋ 한번 해보자 음료에 변수들이 존재하는지 확인하는 메소드를 만들자.

토핑에 대한 새로운 부울 값을 추가하고, Beverage의 cost()를 구현하면 토핑들의 가격을 구할수 있어!
하위 클래스가 계속해서 Override 될거고, cost()에서 음료 비용과 토핑 비용을 계산할 수 있도록 슈퍼를 호출할거야!

![에에에에](https://lh5.googleusercontent.com/1AWm5rJSmCmfZfVf6dtlTs7qTeh02KXhUk_guORxOL0ayCuHI5YymQIKpW8aKtaw-G--S60dcLSHV-CAkxKxMVo0AQJCwGi50kIXk9tgAPyJxni7mlXroxmvgCBJF2clzKsoDNos)

이것들은 토핑에 대한 부울값을 얻고 겟셋하는 메소드들이야 (hasmilk(), setMilk())

### P.121
이제 서브클래스를 추가할게, 메뉴의 각 음료: 슈퍼 클래스의 cost()는 모든 토핑들의 비용을 계산하고 서브 클래스의 cost()는 각각의 음료의 비용을 포함하도록 기능을 확장하자.
각각의 cost()는 음료의 비용을 계산한 다음 슈퍼클래스의 cost()를 호출해서 토핑을 추가해줘야 돼.

### P.122
이 설계에 영향을 끼칠 수 있는 요구사항은 무엇이니?
토핑의 가격이 변경되면 우리의 코드도 바뀌어야 돼
새 토핑이 추가되면 또 코드가 바뀌겠지
새 음료가 있을 때의 대처방안도 생각해 봐야 돼 으으..
그리고 고객이 **더블** 모카를 원할 때엔 어떻게 해야되지..

### P.123
> 스승과 제자 대화 페이지

마스터: 제자야.. 상속에 대해 깊게 파고든적있니

제자: 네 마스터, 상속은 강력하지만 유연하지않고 유지관리가 설계로 이어지지 않는 다는걸 알았어요.

마스터: 오 그래, 좀 나아졌구나. 그래서, 상속을 통하지 않고 어떻게 코드를 재사용 할 수 있을까 들어보자꾸나

제자: *composition(구성) 및 delegation(위임)을 통한* 런타임 환경에서 inheriting Behavior(상속행위)하는 방법이 있다는 것을 배웠어요.

마스터: 계속 말해보거라..

제자: 서브클래스에 의해 Behavior를 물려받았을때, Behavior는 컴파일 시간에 정적으로 set(설정)됩니다. 추가적으로, 모든 서브클래스는 같은 행동을 반드시 물려받죠.

하지만 만약, composition(구성)을 통해 객체의 행동(동작)(Behavior)을 확장하는게 가능하다면, 그럼 전 이걸 런타임환경에서 동적으로 행동을 설정할 수 있어요.

마스터: 좋구나 제자야 너는 composition(구성)의 힘을 보기 시작한게로구나.

제자: 네, 이 기술을 통해 객체에 여러가지 새로운 책임을 추가할 수 있어요. 슈퍼클래스의 디자이너조차 생각치 못한 책임을 추가할 수 있어요, 코드를 만질 필요도 없이말이죠.

마스터: composition(구성)이 너의 코드 유지보수에 미치는 영향에 대해 무엇을 배웠니?

제자: 글쎄요, 제가 동적으로 객체를 구성함으로써 얻었던 것들은, 기존 코드를 바꾸는 것이 아니라 새로운 코드를 작성하여 새로운 기능을 추가할 수 있다는 것. 
또, 기존 코드를 변경하지 않았기때문에 기존 코드의 의도하지 않은 부작용을 일으킬 가능성이 훨씬 줄어든다는 것이에요.

마스터: 오늘은 충분하구나 훌륭하다 제자야.. 나는 네가 이 주제에 대해 더 깊이 생각했음 하는구나..
명심하거라 코드는 변화에 대해 폐쇄적이여야 한단다. 마치 저녁의 연꽃과 같이 말이다. 
( code should be closed(to change) like the lotus flower in the evening )

확장을 할때에는 개방적이여야한단다. 아침의 연꽃과 같이 말이다.
( yet open (to extension) like the lotus flower in the morning. )

### P. 124
개방-폐쇄 원칙 ( Open-Closed Principle )
확장할때는 개방적이여야 되고 변경할때는 폐쇄적이여야됨.

> 들어오세요. 영업합니다. 맘에드는 새 행동으로 클래스를 확장해주십쇼.

> 요구사항이 변경되는 경우 새롭게 확장하면 되요.

우리의 목표는 기존 코드를 수정하지 않고 새 behavior를 클래스에 쉽게 확장하는것 입니다. 이 일을 해내면 우리는 변화에 탄력적이고 변화하는 요구사항을 받아낼수 있는 유연한 설계를 얻을 수 있어여.

### P.125
확장해야 하는 코드 영역을 선택할 때는 주의해야 합니다. 개방-폐쇄 원칙을 적용하는 것은 낭비적이고 불필요하며, 복잡하고 이해하기 어려울 수 있어요.

### P.126
![shutupandhelpmeplease](https://lh5.googleusercontent.com/6dAu1SPlwZcNQmnceJKcB5ip08sQWOveXteXiN5BG4re4II_o7jxCg6mJ_hWtOXffzMyul-WGFoUpYebh_94p78drjg1kzf60_dPd756eOFlyeMLlYe0xsaRnkt4xLpDQF4tw5NU)

"빨리 우리나 도와달란 말이에요!"

## 데코레이터 패턴을 만나보시죠
우린 상속을 이용한 음료(beverage) 가격 더하기 책정이 잘 되지 않았다는 걸 알게 되었어여.

클래스 폭발(class explosions)이나 단단한 디자인?(rigid designs), 일부 서브클래스에 적합하지않은 기능들을 추가했습니다.

자, 우리가 대신 할일은 다음과 같은 것들이에요:

음료(Beverage)를 런타임에 토핑들로하여금 꾸며줄것(Decorate)이에요.
예를 들면, 손님이 모카, 휘핑크림을 올린 다크 로스트를 원하면은 다음과 같은 작업을 하는 것이죠.

 1.  DarkRoast 객체를 가져오기(Take).
 2.  Mocha(Sub Object) 객체로 DarkRoast객체 꾸미기(Super Object)
 3.  Whip(Mocha의 Sub) 객체로 Mocha(Whip의 Super, DarkRoast의 Sub) 객체 꾸미기.
 4.  cost() 메소드를 호출하고 위임(delegation)에 의존하여 토핑 가격을 추가한다.

좋아여! 그런데 어떻게 객체를 **“꾸미고(decorate)”** , 어떻게 **위임(delegation)** 을 하는거죠?

힌트 : 장식자 객체(decorator object)를 랩퍼라고 생각해보세요.

어떻게 작동하는지 한번 봐 볼까요?

### P. 127
데코레이터와 함께 음료 만들기

1. DarkRoast 객체로 시작합니다. - DarkRoast는 Beverage Class를 구현 해서 음료의 가격을 계산해주는 cost() 메소드가 있다는 걸 기억해야 해
2. 손님이 모카를 원하면 모카 객체를 생성하고 DarkRoast를 감싸주는 거지 - Mocha는 데코레이터고 타입은 Beverage야 그러니, Mocha 객체도 cost() 메소드를 갖고 있어. 그리고 다형성을 통해 Mocha에 싸인(Wrapped) 어떤 종류의 음료도 다룰 수 있어 (왜냐면 Mocha는 Beverage의 서브타입이니까)
3. 그리고나서 손님이 휘핑크림을 원할 경우, Whip 데코레이터를 만들어서 Mocha를 감싸! Whip도 데코레이터야 얘도 cost()메소드를 갖고 있지 왜냐면 Beverage 타입이니까
4. 자 이제 가격을 계산해서 손님에게 보여줄 시간이야. 가장 바깥의 데코레이터의 cost()를 불러서 할거야. cost를 객체에게 위임하는거지, cost가 있다면 그만큼의 비용을 더해줄거야

---

![럽송노요리키라메키](https://lh5.googleusercontent.com/1RWvPUDQbwTB0YYdENITLytkWHCbLf9xAqgbiBj9pSXBFS5xOgN9ojTPd8OJdpl1sNc2rkHa-06YqWjqjLL4g7eo0G4VMTXK1rVccEP0jDOedTEB-VeN-99mnophhBGLJr8f2j8E)

1. 가장 바깥쪽의 데코레이터인 Whip의 cost()를 부름
2. Whip은 cost()로 Mocha를 부름
3. Mocha는 cost()로 DarkRoast를 부름
4.  DarkRoast는 99센트의 가격을 반환
5.  Mocha는 그것에다 20센트를 더함
6. Whip은 거기다 또 10센트를 더함 총 1.29$

이제 우리가 알 수 있는 사실은..
> 데코레이터는 데코레이터들이 꾸미는 객체와 같은 슈퍼타입을 가지고 있음

> 하나 이상의 데코레이터를 써서 객체를 포장하는게 가능, 객체들은 언제든지 꾸며질 수(Decorated)있어서, 원하는 만큼의 데코레이터와 함께 런타임 환경에서 동적으로 객체들을 꾸밀 수 있음

이제 데코레이터 패턴의 정의를 살펴보고 어떻게 작동하는지 알아보자!

### P.129
데코레이터 패턴 정의
*데코레이터 패턴은 객체에 동적으로 추가 책임을 부여합니다. 
데코레이터는 기능 확장을 위해 서브클래스에 대한 명백한 대안을 제공합니다.*

![데코레이터 패턴 클래스 다이어그램](https://lh3.googleusercontent.com/rSeiS350Dz58yJTKPxIAHZOUDB4lUhkUvIa2EJUlh3Jl5-Lq_u3uRWuqiXLOgGIl9EEa4a8bvRdFUdgAAALhS4gWv6PILAJO5IXtmOIvZhydEWp5L5W4Wtcbhw3JvuwyCoddhSkx)

자기 소유 or 데코레이터에 의해 포장된 Compenent는 해당 구성요소로 사용 할 수 있습니다.
Concrete 컴포넌트는 우리가 동적으로 새로운 행동(Behavior)을 추가할 대상이에요. 컴포넌트를 확장하죠.
각 데코레이터 Has-a 는 컴포넌트를 참조를 포함하는 인스턴스 변수 를 의미합니다.

데코레이터는 그들이 꾸밀(decorate) 구성요소와 동일한 인터페이스나 추상 클래스를 구현해요.
ConcreteDecorator는 꾸민 것(Decorator Object)에 대한 인스턴스 변수가 있어여

데코레이터는 구성 요소(state of the component)의 상태를 확장할 수 있어요.

데코레이터는 새 메소드 추가 가능하지만, 새 행동은 일반적으로 구성요소의 기존 방법 이전 또는 이후에 계산을 수행하여 추가됩니다.

### P.130
Beverage는 추상클래스입니다!
커피의 종류당 하나씩 총 4가지의 콘크리트 부품이 있어요.
그리고 토핑 데코레이터가 있어요.

BrainPower - [ 더 나아가기 전, 커피와 토핑 의 메소드 구현방법에 대해 생각해보셈]

### P.131
상속과 구성(composition)에 관한 혼란.

Mary : 조금 혼란스러워요. 저는 구성(Composition)을 사용할 것이라고 생각했고, 상속을 사용하지 않을 것이라고 생각했어요

Sue: 뭔 소리임

Mary: 다이어그램 봐바 토핑데코레이터는 Beverage클래스에서 확장(extending)됐어 저건 상속이야 그치?

Sue: 그러게, 내생각엔 데코레이터들이 꾸미는 타입들이 같아서 얻게되는 점이라고 생각함 
그래서 우리는 타입을 일치시키기 위해 상속을 한거지, 행동들을 얻기위해 상속을 사용하면 안됨.

### P.133
코드

### P.143
상속은 확장의 한 형태이지만 반드시 설계를 융통성 있게 만드는 좋은 방법은 안다.

우리의 디자인에서 기존의 코드를 수정할 필요없이 행동(Behivor)를 확장할 수 있어야 한다.

구성과 위임(Composition and delegation)을 사용하여 런타임 환경에서 새 행동을 사용 할 수 있게되었다.