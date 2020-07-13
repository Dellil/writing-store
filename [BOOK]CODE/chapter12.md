# CODE Chapter.12 이진 덧셈기
### 이진 덧셈기 

 - 이진수로만 동작
 - *~~총 8비트까지만 더할 수 있게 만들기~~* 라고 책에는 나와있지만 귀찮아서 4비트까지만 만듦

덧셈기 만드는 순서
- XOR 알아야 됨
- XOR과 AND를 쓰까덮밥해서 반가산기(Half-Adder) 만들어야됨
- 반가산기 2개와 OR 쓰까덮밥해서 가산기 만들어야됨

## XOR
| A | B | OR | NAND | AND
|:--:|:--:|:--:|:--:|:--:|
|0|0|0|1|0|
|0|1|1|1|1|
|1|0|1|1|1|
|1|1|1|0|0|
조합으로

| A | B | XOR |
|:--:|:--:|:--:|
|0|0|0|
|0|1|1|
|1|0|1|
|1|1|0|
을 만들면 됨
![xor구성도](https://user-images.githubusercontent.com/42995061/87292411-bc3bcf00-c53b-11ea-94b3-dfe892850f22.jpg)
이렇게 하면 xor가 만들어짐

## 반가산기 만들기
![반가산기게이트그린거](https://user-images.githubusercontent.com/42995061/87292481-d5448000-c53b-11ea-87ef-5853db7c47eb.jpg)
XOR와 AND로 반가산기 만들기
결괏값으로 이진수 덧셈의 합과 자리올림이 나온다.
![반가산기구조설명](https://user-images.githubusercontent.com/42995061/87292812-556ae580-c53c-11ea-8b0a-8770fff6c4d4.jpg)

반가산기로는 여러자릿수의 덧셈을 할 수 없으므로 이걸 쓰까덮밥해서 전가산기를 만들어야 한다.
## 전가산기 만들기
![전가산기 구조](https://user-images.githubusercontent.com/42995061/87292886-73384a80-c53c-11ea-9a85-52645c84a028.png)
3개의 입력이 있음
자리올림 표시, A입력, B입력
출력은 덧셈의 합과 자리올림
![전가산기구조설명](https://user-images.githubusercontent.com/42995061/87292976-96fb9080-c53c-11ea-84c6-42d658500162.jpg)
이제 4비트 덧셈기를 만들어보자
## 4비트 덧셈기
![4비트덧셈기](https://user-images.githubusercontent.com/42995061/87292996-9f53cb80-c53c-11ea-92f5-69ce7f745827.png)
사용한 프로그램 : logisim