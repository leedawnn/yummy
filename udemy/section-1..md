# Section 1. 변수 다루기

## var를 지양하자.

### 왜? 지양해야함?

* `var`는 함수 스코프
* `let`, `const`는 블록 단위 스코프, TDZ (이것들이 코드를안전하게 만들어줌)
  * `const`가 더 안전하다 (재할당x)

### function scope vs block scope

```javascript
var global = '전역';

if (global === '전역') {
    var global = '지역';
    
    console.log(global); // 지역
}

console.log(global); // 지역 (let으로 바꿨을 때, 전역으로 찍힘)
```

의도는 함수 안에서만 `global`이 '지역'으로 찍혀야하는데 모든 `global`변수가 오염되었다..&#x20;

이때, `let`으로 코드를 변경하면 함수 안에 있는 global만 값이 바뀌게 된다.

### 근데, const 사용하면 객체 다룰 때 힘들지 않나?

그렇지 않다,, 다음 예제를 보자.

```javascript
const person = {
    name: 'jang',
    age: '30',
};

// Assignment to constatnt variable. 당연히 재할당은 에러
person = {
    name: 'jang2',
    age: '30',
};

person.name = 'lee';
person.age = '22';

console.log(person); // { name: 'lee', age: '22' }
```

`const`를 사용해도 객체에 데이터를 변경할 수 있다.&#x20;



그럼 배열은 어떻게 될까? 배열 안에 객체를 추가해보자.&#x20;

```javascript
const person = [{
    name: 'jang',
    age: '30',
}];

// Assignment to constatnt variable. 당연히 재할당은 에러
person.push = [{
    name: 'lee',
    age: '22',
}];

console.log(person); // [ { name: 'jang', age: '30' }, { name: 'lee', age: '22' } ]
```

배열 안에 데이터를 추가하는 것도 가능하다.

즉, `const`는 재할당만 금지된다.

## 전역 공간 사용 최소화

* 전역 공간 => 최상위 객체,
  * Window (브라우저 환경)
  * global (NodeJs 환경)

### 전역 공간을 사용하지 마라?

규모가 있는 애플리케이션을 만들 때 체감할 수 있다,, ~~난 아직 체감하지 몬함~~

실험을 하나 해보자. 파일이 나뉘면 스코프는 나뉠까?&#x20;

```javascript
// index.js
var globalVar = 'global';

console.log(globalVar); // global

// index2.js
console.log(globalVar); // global
```

아이러니하게  파일이 나뉘어도 스코프는 나뉘지 않는다...... 💩



더 안 좋은 사례를 보자..!

```javascript
// index.js
var globalVar = 'global';

console.log(globalVar); // global

var setTimeout = 'setTimeout';

// index.js2
setTimeout(() => {
    console.log('1초');
}, 1000) // Uncaught TypeError: setTimeout is not a function
```

`setTimeout`에 문자열을 할당하면서 브라우저 Web API는 함수가 아니게 되어버렸다,,

### 결론

* 전역 공간을 사용하면 어디에서나 접근할 수 있다.
  * 개발자 입장에서 분리되어 있다고 생각하지만 사실 런타임 환경에서는 분리가 되어 있지 않다.&#x20;
* **해결 방법 😊**
  * **전역 변수를 사용하지 않는다.**&#x20;
  * **지역 변수만 사용한다.**
  * **`Window` , `global`을 조작하지 않는다.**
  * **`const`와 `let`을 사용하는 것만으로도 많은 부분이 해소될 수 있다.**
  * **근본적으로는 `IIFE`(즉시 실행 함수), `Module`, `클로저`, `스코브 분리`하는 방법이 있다.**

## 임시 변수 제거하기

* 임시 변수란?
  * 코드의 특정 부분에서만 사용되고 그 이후에는 더 이상 참조되지 않는 변수를 말한다.
  * 예를 들어, 스코프 안에서 전역 변수처럼 활용되는 친구들이 있다.

```javascript
function getObject() {
    const result = {};
    
    result.title = document.querySelector('.title');
    result.text = document.querySelector('.text');
    result.value = document.querySelector('.value');
    
    return result;
}
```

