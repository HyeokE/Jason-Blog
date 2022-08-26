---
title: call, apply, bind
date: 2022-06-28 15:06:29
category: JavaScript
thumbnail: { thumbnailSrc }
draft: false
---


>이전 글에 이어서 작성되는 글입니다.

# 명시적으로 this를 바인딩하는 방법

## call 메서드

call은 모든 함수에서 사용이 가능하고 this를 특정 객체로 지정이 가능해요.

`call(this, 함수에 전달할 인자)`
call은 첫번째 인자로 this, 두 번째 인자로 함수의 파라미터를 받아요.


```js
let func = function(a,b,c){
  console.log(this, a,b,c);
};

func(1,2,3);
func.call({x:1}, 1,2,3);
```

위 예시를 실행시켰을 때 func의 this는 window가 출력되고 call로 실행한 func의 this는 {x: 1}이 출력돼요

다른 예를 봅시다.
```js
let obj = {
 a:1;
 method: func(a, b){
 	console.log(this.a, x, y);
 }
}
obj.method(2, 3);
obj.method.call({x:1}, 4, 5);
```
call은 객체의 함수에서도 사용이 가능합니다.


## apply

call과 기능은 같지만 함수의 파라미터는 배열로 받는다는 차이점이 있다.

```js
let func = function(a,b,c){
  console.log(this, a,b,c);
};

func(1,2,3);
func.apply({x:1}, [1,2,3]);
```

>파라미터를 배열로 받으면 어떤 점이 좋죠?

예를 들어 배열 내에서 가장 작은 수를 출력하는 min 함수를 사용해볼게요.
이 배열의 값을 그대로 전달하고 싶다면 사용하는 것이죠
```js
const arr = [1,2,5,6,9];
const minValue = Math.min.apply(null, arr);
console.log(minValue)
```


## bind

bind는 call과 비슷하지만 즉시 호출하지는 않고 새로운 함수를 return해줍니다.

```js
let func = function (a, b, c, d) {
  console.log(this, a,b,c,d);
};
func(1, 2, 3, 4);

let funcBind = func.bind({x:1});

funcBind(5, 6, 7, 8);

let funcBind2 = func.bind({x:1}, 4, 5);

funcBind2(6, 7);
funcBind2(8, 9);
```

### name 프로퍼티

bind 메서드를 이용해서 새로 만든 함수는 독특한 성질이 있는데 name 프로퍼티에 bound라는 접두어가 붙습니다.

어떤 함수의 name 프로퍼티가 bound로 시작한다면 bind를 적용한 함수라는 뜻이 되므로 call이나 apply보다 코드를 추적하기 더 쉽습니다.

```js
let func = function (a, b, c, d) {
  console.log(this, a,b,c,d);
};

let funcBind = func.bind({x:1});

console.log(funcBind.name); //boung funcBind

```


### 상위컨텍스트의 this를 콜백함수에 전달하기

call을 이용한 방법
```js
let obj = {
  outer: function () {
    console.log(this);
    let innerFunc = function () {
      console.log(this);
    }
    innerFuc.call(this);
  }
}
obj.outer();
```

bind를 이용한 방법

```js
let obj = {
  outer: function () {
    console.log(this);
    let innerFunc = function () {
      console.log(this);
    }.bind(this);
    innerFunc();
  }
}
obj.outer();
```

콜백을 인자로 받는 함수나 메서드 중에서 this에 관려하는 함수나 메서드에 대해서도 blind를 활용하면 this를 자유롭게 바꿀 수 있습니다.