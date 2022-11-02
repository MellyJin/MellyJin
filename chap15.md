# Chapter 15, 타임라인 격리하기


## 💡 타임라인 다이어그램을 그려보고, 다이어그램으로 버그 찾아내기


❔ 문제 상황: 장바구니당 배송비가 한번만 추가되어야하는데, 빠르게 두 번 클릭시, 배송비가 2번 계산된다.
- 왜 이런 문제가 발생했는지, 타임라인 다이어그램을 그려 확인해보자.
![image](https://user-images.githubusercontent.com/114047824/199459087-c047c922-463b-4932-8ae2-581236d0b9ba.png)

# 

## 타임라인 다이어그램 기본 규칙 
1. 두 액션이 <u>**순서대로**</u> 나타나면, **같은 타임라인**에 넣는다.
2. 두 액션이 **동시에 실행**되거나 **순서를 예상할 수 없다**면, **분리된 타임라인**에 넣는다.

ex) 저녁 준비하기
```
function dinner(food) {
  cook(food);
  serve(food);
  eat(food);
}
```
![image](https://user-images.githubusercontent.com/114047824/199459352-a99dfc02-bd37-4cb3-872f-7a5775cd42e8.png)


여러 명이 동시에 저녁을 먹는다면?
![image](https://user-images.githubusercontent.com/114047824/199459296-0512b62c-2a99-402a-a236-da41252a87e6.png)
 

## 타임라인 그리기

### 1단계: 액션을 확인한다.
유의사항 
- ++와 +=는 3단계다
- 인자는 함수를 부르기 전에 실행된다.


### 2단계: 순서대로 실행되거나, 동시에 실행되는 액션 그리기
비동기 호출은 새로운 타임라인으로 그린다.

|유형|언어|특징|
|-----|-----|-----|
|단일 스레드, 동기|||
|단일 스레드, 비동기|||
|멀티스레드|||
|메시지 패싱|||

순서가 섞일 수 있는 코드 VS 순서가 섞이지 않는 코드
- 순서가 섞인다? 순서대로 실행되는 두 액션 사이에 다른 타임라인에 있는 액션이 끼어드는 것

자바스크립트비동기큐, AJAX 이벤트큐 ??

### 3단계: 단순화하기
- 하나의 타임라인에 있는 모든 액션을 하나로 통합
- 타임라인이 끝나는 곳에서 샐운 타임라인이 하나만 생기면 통합

## 좋은 타임라인
- 타임라인을 적게
- 타임라인을 짧게
- 공유하는 자원을 적게
- 자원을 공유할 땐 서로 조율하고
- 시간을 일급으로 다룬다. -> 다른 장에서 자세히..
![image](https://user-images.githubusercontent.com/114047824/199459809-42d37fcc-c1b1-4cca-82bf-b8c4ba329285.png)


해당 문제 해결법
1. 공유하는 자원 없애기
  - 전역변수를 지역변수로
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
  - 전역변수를 인자로
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

+ 재사용하기 좋은 코드 만들기 -> 함수 본문을 콜백으로 바꾸기
+ 비동기 호출에서 명시적인 출력을 위해 리턴값 대신, 콜백을 사용할 수 있다

-----

질문/소감
|번호|이름|소감/질문|
|---|---|-------|
|1|최경환|단일 쓰레드에서도 타임라인에 따라 sync가 안맞을 수 있다는 좋은 설명이었던 것 같습니다.|
|2|김학진|분산시스템에서도 가장(?) 많이 이야기가 나왔던 '시간'에 대한 부분을 알기 쉽게 설명된 듯 합니다. 결국은 전역변수를 사용하는 액션을 최대한 제거하는 것이 가장 중요한 것을 다시금 느꼈습니다.|
|3|이우진|동작들이 수행되는 순서를 알고, 동작간의 영향을 알고, 이러한 것들에 대해서 타임라인으로 나타내면 순서가 바뀌어서 나타나는 문제에 대해서 파악하기 좋을 것 같습니다. 클릭 두번을 시간에 따라 예를 들어서 자세하게 설명된 점이 좋았습니다.|
|4|||
