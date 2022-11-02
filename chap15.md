# Chapter 15, 타임라인 격리하기


## 💡 타임라인 다이어그램을 그려보고, 다이어그램으로 버그 찾아내기


❔ 문제 상황: 장바구니당 배송비가 한번만 추가되어야하는데, 빠르게 두 번 클릭시, 배송비가 2번 계산된다.
|정상 상황|문제 상황|
|-------|-------|
|![image](https://user-images.githubusercontent.com/114047824/199532365-1c4bc04b-0bd2-4b3d-8222-036960b93355.png)|![image](https://user-images.githubusercontent.com/114047824/199532391-8e4151b3-28cf-4d61-ae5b-ebb07e4ac73c.png)|

- 왜 이런 문제가 발생했는지, 타임라인 다이어그램을 그려보고 다이어그램을 통해 확인해보자.

-----

### 타임라인 다이어그램 기본 규칙 
1. 두 액션이 <u>**순서대로**</u> 나타나면, **같은 타임라인**에 넣는다.
2. 두 액션이 **동시에 실행**되거나 **순서를 예상할 수 없다**면, **분리된 타임라인**에 넣는다.

ex) 저녁 준비하기, 요리하고 먹기까지의 과정은 순서대로 이루어진다. -> 같은 타임라인
```
function dinner(food) {
  cook(food);
  serve(food);
  eat(food);
}
```
![image](https://user-images.githubusercontent.com/114047824/199459352-a99dfc02-bd37-4cb3-872f-7a5775cd42e8.png)


여러 명이 동시에 저녁을 먹는다면? 각 사람들의 식사는 어떤 순서로 이뤄질지 알 수 없다. -> 분리된 타임라인
![image](https://user-images.githubusercontent.com/114047824/199459296-0512b62c-2a99-402a-a236-da41252a87e6.png)
 

## ⭐ 타임라인 그리기 ⭐

### 1단계: 액션을 확인한다.
참고사항 
- ++와 +=는 3단계다
- 인자는 함수를 부르기 전에 실행된다.

ex) 장바구니 예제
![image](https://user-images.githubusercontent.com/114047824/199527251-e6f6159c-1e52-4510-b61e-285f88c02b34.png)
![image](https://user-images.githubusercontent.com/114047824/199529112-559c15c0-4ad2-403f-a089-1d998659b87f.png)


### 2단계: 순서대로 실행되거나, 동시에 실행되는 액션 그리기
비동기 호출은 새로운 타임라인으로 그린다.

ex) 장바구니 예제
![image](https://user-images.githubusercontent.com/114047824/199529324-66e221a1-638a-42f2-9d44-1a89453aceb5.png)

|유형|언어|특징|
|-----|-----|-----|
|단일 스레드, 동기|php|모든 것이 순서대로 실행, 입출력을 사용하면 끝날때까지 기다려야 한다, 시스템이 단순하다는 장점|
|단일 스레드, 비동기|javascript|입출력 작업을 위해 비동기 모델 사용, 입출력 결과는 콜백으로 받을 수 있다|
|멀티스레드|Java, Python, Ruby, C, C# ...|실행 순서를 보장하지 않아 프로그래밍하기 어려움|
|메시지 패싱|Elixir, Erlang|서로 다른 프로세스를 동시에 실행할 수 있는 스레드 모델 지원, 메모리를 공유하지 않고 메시지로 통신 서로 다른 타임라인에 있는 액션이 순서가 섞이더라도, 메모리를 공유하지 않아 문제가 되지 않음|

![image](https://user-images.githubusercontent.com/114047824/199525635-75c80643-7646-4ddc-af97-9c06ca9ba3b7.png)

순서가 섞일 수 있는 코드 VS **순서가 섞이지 않는 코드** 👍

![image](https://user-images.githubusercontent.com/114047824/199529534-15e7d362-1004-46df-93ba-774a28ed5e9f.png)

- 순서가 섞인다? 순서대로 실행되는 두 액션 사이에 다른 타임라인에 있는 액션이 끼어드는 것

가능한 실행 순서가 많을수록 결과를 예측하기 어려워진다.
![image](https://user-images.githubusercontent.com/114047824/199529610-e461d751-2e4f-4a52-8349-1caf79ec2d57.png)


> 자바스크립트 비동기큐 - 자바스크립트는 작업큐라는 큐를 가지고 이벤트 루프를 통해 작업을 처리한다. 비동기 동작을 호출하면 작업큐에 콜백이 추가된다. 
> AJAX(Asynchronous Javascript And XML) - 브라우저에 기반을 둔 웹 요청. 자바스크립트에서 AJAX요청을 만들면 네트워크 엔진이 요청 큐에 AJAX 요청을 넣는다. 비동기이므로 요청이 완료될 때까지 기다리지 않고 다음 작업을 진행한다.


### 3단계: 단순화하기
- 하나의 타임라인에 있는 모든 액션을 하나로 통합
- 타임라인이 끝나는 곳에서 새로운 타임라인이 하나만 생기면 통합

|통합 전|통합 후|
|-----|-----|
|![image](https://user-images.githubusercontent.com/114047824/199536007-316a5e9d-448f-4a48-a7a4-0b109542bf16.png)|![image](https://user-images.githubusercontent.com/114047824/199530403-3e101fd1-c650-41b1-92e3-054313c1289e.png)|


## 좋은 타임라인
- 타임라인을 *적게*
- 타임라인을 *짧게*
- *공유하는 자원을 적게*
- 자원을 공유할 땐 서로 조율하고
- 시간을 일급으로 다룬다? -> 다른 장에서 자세히..(다음발표자에게)

![image](https://user-images.githubusercontent.com/114047824/199459809-42d37fcc-c1b1-4cca-82bf-b8c4ba329285.png)

#

## > 장바구니 배송비 계산 문제 원인 발견
|정상 상황|문제 상황(빠르게 클릭)|
|-------|-------| 
|![image](https://user-images.githubusercontent.com/114047824/199531028-5565be8d-7bfd-418c-b503-faf49c6c0dfd.png)|![image](https://user-images.githubusercontent.com/114047824/199530827-8999cb5e-2c5f-4d46-98dc-47ce39ec4acf.png)|

-----


## 해결 방법
### 공유하는 자원 없애기
![image](https://user-images.githubusercontent.com/114047824/199530745-dddf3167-d11e-44d2-961b-47eacb48c0a9.png)

  #### 1. 전역변수를 지역변수로
  지역변수로 바꿀 수 있는 전역변수를 찾고,
   ```
  function calc_cart_total() {
    total = 0;
    cost_ajax(cart, function(cost) {
        total += cost;
        shipping_ajax(cart, function(shipping) {
            total += shipping;
            update_total_dom(total);
        });
    });
}
 ```
  바꾼다
  ```
  function calc_cart_total() {
    var total = 0;
    cost_ajax(cart, function(cost) {
        total += cost;
        shipping_ajax(cart, function(shipping) {
            total += shipping;
            update_total_dom(total);
        });
    });
}
  ```
|변경 전|변경 후|
|-----|-----|
| ![image](https://user-images.githubusercontent.com/114047824/199462072-0a8a9409-6fd9-4ac8-adc7-64a969d8270c.png)|![image](https://user-images.githubusercontent.com/114047824/199462017-68790e50-64c3-4d8c-84ad-5c125e03fd55.png)|

  
  #### 2. 전역변수를 인자로
  암묵적 인자를 확인하고,
  ```
  function add_item_to_cart(name, price, quantity) {
    cart = add_item(cart, name, price, quantity);
    calc_cart_total();
}
function calc_cart_total() {
    var total = 0;
    cost_ajax(cart, function(cost) {
        total += cost;
        shipping_ajax(cart, function(shipping) {
            total += shipping;
            update_total_dom(total);
        });
    });
}
```
인자로 바꾼다.
```
function add_item_to_cart(name, price, quantity) {
    cart = add_item(cart, name, price, quantity);
    calc_cart_total(cart);
}
function calc_cart_total(cart) {
    var total = 0;
    cost_ajax(cart, function(cost) {
        total += cost;
        shipping_ajax(cart, function(shipping) {
            total += shipping;
            update_total_dom(total);
        });
    });
}
```
|변경 전|변경 후|
|-----|-----|
|![image](https://user-images.githubusercontent.com/114047824/199461351-39ea5ed5-3c45-4c0c-8313-b0860da8c26c.png)|![image](https://user-images.githubusercontent.com/114047824/199461502-61dece8c-45ef-4b06-a03c-428679244254.png)|

-----

추가 내용
+ 재사용하기 좋은 코드 만들기 -> 함수 본문을 콜백으로 바꾸기
+ 비동기 호출에서 명시적인 출력을 위해 리턴값 대신, 콜백을 사용할 수 있다

-----
### Q. 연습문제
> 1. 두 타임라인은 자원을 공유할 수 있다. <span style="color:black"> O </span>
> 2. 자원을 공유하는타임라인이 자원을 공유하지 않는 타임라인보다 안전하다. <span style="color:black"> X </span>
> 3. 같은 타임라인에 있는 두 액션은 서로 자원을 공유하지 않도록 해야 한다. <span style="color:white"> X </span>
> 4. 계산은 타임라인에 그릴 필요 없다. <span style="color:white"> O </span>
> 5. 같은 타임라인에 있는 두 액션은 동시에 실행된다. <span style="color:white"> X </span>
> 6. 자바스크립트는 스레드가 하나이기 때문에 타임라인을 무시해도 된다. <span style="color:white"> X </span>
> 7. 서로 다른 타임라인에 있는 두 액션은 동시에 실행되거나 왼쪽 먼저, 오른쪽 먼저 실행될 수 있다. <span style="color:white"> O </span>
> 8. 공유하는 전역변수를 없애는 방법은 인자나 지역변수를 사용하는 것이다. <span style="color:white"> O </span>
> 9. 타임라인 다이어그램은 소프트웨어가 동작할 때 실행 가능한 순서를 이해하기 좋다. <span style="color:white"> O </span>
> 10. 자원을 공유하는 타임라인은 타이밍 문제가 생길 수 있다. <span style="color:white"> O </span>
-----

### :ok_woman: 질문/소감
|번호|이름|소감/질문|
|---|---|-------|
|1|화니|단일 쓰레드에서도 타임라인에 따라 sync가 안맞을 수 있다는 좋은 설명이었던 것 같습니다.|
|2|스시|분산시스템에서도 가장(?) 많이 이야기가 나왔던 '시간'에 대한 부분을 알기 쉽게 설명된 듯 합니다. 결국은 전역변수를 사용하는 액션을 최대한 제거하는 것이 가장 중요한 것을 다시금 느꼈습니다.|
|3|젤리|동작들이 수행되는 순서를 알고, 동작간의 영향을 알고, 이러한 것들에 대해서 타임라인으로 나타내면 순서가 바뀌어서 나타나는 문제에 대해서 파악하기 좋을 것 같습니다. 클릭 두번을 시간에 따라 예를 들어서 자세하게 설명된 점이 좋았습니다.|
|4|티모|복잡한 상황에서 발생할 수 있는 오류를 타임라인 다이어그램을 통해 찾아낼 수 있다는 것을 배웠습니다.|
