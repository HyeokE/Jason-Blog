---
title: this란 무엇인가.
date: 2022-06-27 15:06:29
category: JavaScript
thumbnail: { thumbnailSrc }
draft: false
---



자바스크립트에서 가장 어려운 개념을 뽑으라고 한다면 저는 `this`와 실행컨텍스트를 선택할 것같은데요.

이번 post에서는 `this`의 개념에 대해 알아보도록 해요.

# 상황에 따라 달라지는 this

자바스크립트의 `this`는 기본적으로 실행컨텍스트가 생성될 때 함께 결정돼요.

실행 컨텍스트는 함수가 실행될 때 결정되므로 다르게 말하면 `this는 함수가 실행될 때 결정된다.` 라고 할 수 있겠네요.

함수를 어떻게 호출하는지에 따라 `this`값이 달라지는 것입니다.

## 전역공간에서의 this

전역공간에서의 this는 전역객체를 가르켜요.
개념상 전역컨텍스트를 생성하는 주체가 전역객체이기 때문이에요.

전역객체는 실행되는 환경에서 다른 이름과 정보를 가지고 있는데요.
브라우저 환경에서는 `window`, node.js 환경에서는 `global`이라는 이름을 가지고 있어요.

**브라우저환경**
```js
console.log(this); // window
console.log(this === window); //true
```

**Node.js 환경**
```js
console.log(this); // global
console.log(this === global); //true
```

>**전역객체의 프로퍼티**
>
> 만약 전역변수를 선언한다면 자바스크립트 엔진은 전역객체의 프로퍼티로도 할당해요.
>
> 변수이면서 객체의 프로퍼티인 것이죠.
>
```js
var a = 1;
console.log(this.a); // 1
console.log(a); // 1
console.log(window.a); // 1
```

## 메서드로서 호출할 때 메서드 내부에서의 this

### 함수 vs 메서드

함수를 실행하는 방법은 여러가지가 있는데 일반적인 방법으로 함수로서 호출하는 방법, 메서드로서 호출하는 방법이 있어요.

이 둘의 차이는 독립성인데요. 함수는 그 자체로 독립적인 기능을 수행하는 반면, 메서드는 자신을 호출한 대상 객체에 관한 동작을 수행해요.


**함수로서 호출, 메서드로서 호출**
```js
var func = function(x) {
    console.log(this, x);
}
func(1); //window, 1
var obj = {
    method: func,
}
obj.method(2); //method, 2
```

1번째 줄에서 `func`이라는 변수에 익명함수를 할당했습니다.
4번째 줄에서 `func`를 호출했더니 `this`로 `window`가 출력됩니다.
6번째 줄에서 `obj`라는 변수에 객체를 할당하는데 `method`에 `func`함수를 할당했어요.
9번째 줄에서 `obj`의 `method`를 호출하니 이번에는 `this`가 `obj`를 가르키네요.

익명함수를 변수에 담아 호출한 경우와 `obj` 객체에 담아 호출하는 경우에 `this`값이 달라지는 것이죠.

>**함수로서의 호출과 메서드로서의 호출을 구분하는 방법**
>
> 함수 앞에 `.`이 있는지 여부로 간단하게 구분할 수 있어요.
>
> 위 예시에서 4번째 줄은 앞에 점이 없으니 함수로서 호출한 것이고 9번째 줄은 method 앞에 점이 있으니 메서드로서 호출한 것이죠.
>
> 물론 대괄호 표기법에 대한 경우에도 메서드로서 호출한 것이에요.

**메서드로서 호출 - 점 표기법, 대괄호 표기법**
```js
var obj = {
    method: function(x) {
        console.log(this, x);
    }
}
obj.method(1); //obj, 1
obj['method'](2); //obj, 2
```

정리하자면 어떤 함수를 호출할 때 그 함수 이름 앞에 객체가 명시되어있는 경우 메서드로 호출한 것이고 그렇지 않은 경우는 함수로 호출한 것이에요.

### 메서드 내부에서의 this

`this`에는 호출한 주체에 대한 정보가 담겨있어요.

어떤 함수를 메서드로서 호출한 경우 호출주체는 함수명 앞의 객체입니다.

점 표기법의 경우 점 앞에 명시된 객체가 `this` 인 것이죠.


## 함수로서 호출할 때 그 함수 내부에서의 this

### 함수 내부에서의 this
어떤 함수를 함수로서 호출할 경우 `this`가 지정되지 않아요. 앞서 설명했듯이 `this`에는 호출한 주체에 대한 정보가 담기는데
함수로서 호출하는 것은 호출주체를 정하지 않고 개발자의 관여로 실핼할 것이기 때문에 정보를 알 수 없는 것이에요.

실행컨텍스트가 생성될 당시 `this`가 지정되지 않은 경우 `this`는 전역객체를 바라봅니다.
따라서 함수에서의 `this`는 전역객체입니다.

>더글라스 크락포드는 이를 명백한 설계상의 오류라고 지적했어요.
>
> [더글라스 크락포드 - 자바스크립트 제작자 중 한 명](https://ko.wikipedia.org/wiki/%EB%8D%94%EA%B8%80%EB%9D%BC%EC%8A%A4_%ED%81%AC%EB%A1%9D%ED%8F%AC%EB%93%9C)

### 메서드의 내부함수에서의 this

`this`라는 단어 자체가 주는 느낌 그대로 코드를 보면 예상과는 전혀 다른 결과가 나올 때가 많아요.

내부함수 역시 함수로서 호출했는지 메서드로서 호출했는지 파악하면 `this`의 값을 정확히 알 수 있습니다.

아래 예시의 실행순서를 살펴봅시다.

```js
var obj1 = {
    outer: function() {
        console.log(this); //#1
        var innerfunc = function() {
            console.log(this); //#2 , #3
        }
        innerfunc();
        var obj2 = {
            inner: innerfunc,
        }
        obj2.inner(); 
    }
}
obj1.outer();
```

정답을 먼저 보면 `1. obj1`, `2. window`, `3. obj2.inner` 입니다

1. 객체를 생성하며 내부에는 `outer` object가 존재하고 여기에는 익명함수가 연결됩니다. 이렇게 생성한 객체를 변수`obj1`에 할당합니다.
2. `obj1.outer()`를 호출합니다.
3. `obj1.outer` 함수의 실행컨텍스트가 생성되며 호이스팅이 일어나고 스코프 체인 정보를 수집하며 `this`를 바인딩합니다.
   이 함수는 호출될 때 outer 앞에 ` . `이 있으므로 메서드로서 호출한 것입니다.
4. obj1 객체 정보가 출력됩니다.
5. 호이스팅 된 변수 innerfunc에 익명함수를 할당합니다.
6. innerfunc를 호출합니다.
7. innerfunc의 실행컨텍스트가 생성되며 호이스팅이 일어나고 스코프 체인 정보를 수집하며 `this`를 바인딩합니다.
   이 함수를 호출할 때 `.`이 없었으므로 함수로서 호출한 것입니다. 따라서 `this`는 `window`가 바인딩됩니다.

### this를 바인딩하지 않는 함수

`this`가 전역객체를 바라보닌 문제를 보완하고자 `this`를 바인딩하지 않는 화살표함수를 도입했습니다.

```js
let obj = {
    outer: function (){
        console.log(this);
        var innerFunc = () => {
            console.log(this);
        }
        innerFunc();
    }
}
```
이 외에 call, apply 등의 메서드를 활용해 this를 바인딩하는 것도 가능합니다.

## 콜백 함수 호출 시 그 함수 내부에서의 this

함수 A의 제어권을 다른 함수에 넘겨주는 경우 함수 A를 콜백 함수라고 합니다.
콜백 함수도 함수이기 때문에 기본적으로 전역객체를 참조하지만 별도로 this가 될 대상을 지정한 경우 대상을 참조하게 됩니다.

```js
setTimeout(function() {
    console.log(this);
}, 1000);

[1, 2, 3].forEach(function(item) {
    console.log(this);
});

document.body.innerHTML = '<div>클릭</div>';

document.body.querySelector('#a').addEventListener('click', function(e) {
    console.log(this, e);
});
```

setTimeout 함수와 forEach 의 메서드는 내부에서 콜백함수를 호출할 때 대상이 될 this를 지정하지 않습니다.

따라서 콜백 함수 내부의 this는 전역객체를 참조합니다.

## 생성자 함수 내부에서의 this

보통 객체지향 언어에서는 생성자를 클래스, 그 클래스로 만든 객체를 인스턴스라고 해요.
유저에 대한 기본적인 데이터 key를 몇 가지를 떠올려보면 이름, 나이, 성별, 주소, 전화번호 등이 있어요.

각 인스턴스들은 위와 같은 공통점이 있지만 각자의 고유적인 정보도 있을 수 있죠.

```js
const Cat = function (name, age){
    this.name = name;
    this.age = age;
    this.say = function(){
     console.log('애옹');
    }
}
const cat1 = new Cat('나비', 2);
const cat2 = new Cat('냥이', 2);

console.log(cat1, cat2);
```

Cat이란 변수에 익명함수를 할당했습니다. 이 함수 내부에서는 this에 접근해서 say와 name, age 프로퍼티에 각각 값을 대입해요.

new 명령어와 함께 cat1과 cat2를 할당했습니다.

console.log로 cat1과 cat2를 출력해보니 각 catdml 인스턴스 객체가 출력됩니다.
```js
const cat1 = new Cat('나비', 2);
const cat2 = new Cat('냥이', 2);
```
생성자 함수 내부에서의 this는 각각 cat1, cat2의 인스턴스를 가르킨다는 것을 알 수 있어요.

## 명시적으로 this를 바인딩하는 방법

다음 글에서 이어서 작성됩니다.
