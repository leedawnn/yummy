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

* 전역 공간 => 최상위 객체
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
function getElements() {
    const result = {}; // 임시 객체
    
    result.title = document.querySelector('.title');
    result.text = document.querySelector('.text');
    result.value = document.querySelector('.value');
    
    return result;
}
```

위 예제에서 `result`는 임시 변수다. 하지만 굳이 임시 변수를 사용할 필요가 없다. 그럼 어떻게 코드를고칠 수 있을까?

```javascript
function getElements() {
    return {
        title: document.querySelector('.title'),
        text: document.querySelector('.text'),
        value: document.querySelector('.value'),
    }
}
```

훨씬 깔끔해졌다! 딱  봐도 이 코드는 사이드 이펙트가 많지 않아 보인다.



또 하나의 예를 보자.&#x20;

```javascript
function getDateTime(targetDate) {
    let month = targetDate.getMonth();
    let day = targetDate.getDate();
    let hour = targetDate.Hours();
    
    // 월, 날짜, 시간을 여기서 조작하는데 이런 방식은 문제가 생길 수 있다. (접근 로직 최소화)
    month = month >= 10 ? month : '0' + month;
    day = day >= 10 ? day : '0' + day;
    hour = hour >= 10 ? hour : '0' + hour;
    
    return {
        month, day, hour
    }
}
```

다음 예제는 특정 Date object를 받은 다음, 원하는 날짜 형식대로 조작 후 리턴을 시키는 함수다.&#x20;

여기서 추가적인 요구사항이 들어와서 수정을 해야하는 상황이라면?

1. 함수를 새로 만든다.
2. 기존 함수를 수정한다.

이때, 우리는 기존 함수를 수정한다고 해보자. 이 유틸 함수를 다른 파일에서 많이 쓰고 있다면 기존 코드를 해치지 않으면서 추가적인 요구사항도 충족되게 만들어야한다.&#x20;

먼저, `let`을 `const`로 바꾸자. 재할당 가능성을 제거하는 것이 좋으니까!&#x20;

```javascript
function getDateTime(targetDate) {
    const month = targetDate.getMonth();
    const day = targetDate.getDate();
    const hour = targetDate.Hours();
        
    return {
        month: month >= 10 ? month : '0' + month,
        day: day >= 10 ? day : '0' + day,
        hour: hour >= 10 ? hour : '0' + hour,
    }
}
```

그리고 기존 함수에서 객체를 바로 반환시켜버리자.&#x20;

```javascript
function getDateTime(targetDate) {
    const month = targetDate.getMonth();
    const day = targetDate.getDate();
    const hour = targetDate.Hours();
        
    return {
        month: month >= 10 ? month : '0' + month,
        day: day >= 10 ? day : '0' + day,
        hour: hour >= 10 ? hour : '0' + hour,
    }
}

function getDateTime(params) {
    const currentDateTime = getDateTime(new Date());
    
    // 필요한 요구사항에 따라 작성했다치고,,
    return {
        month: currentDateTime.month + '분 전',
        day: currentDateTime.day + '일 전',
        hour: currentDateTime.hour + '시간 전',
    }
}
```

함수를 한 번 더 추상화했기 때문에 재활용할 수 있다.&#x20;

임시 변수라는 유혹에 빠지면, 임시 변수만 계속 내부에서 조작하게 된다.&#x20;



마지막으로 한 가지 예제를 더 보자.

```javascript
function genRandomNumber(min, max) {
    const randomNumber = Math.floor(Math.random() * (max + 1) + min);
    
    randomNumber * 100; // 누가 조작할 위험이 있음
    
    return randomNumber;
}
```

우리는 함수를 만들 때, 함수 내부의 임시 변수에 대한 유혹을 받지 않도록 단 하나의 역할만 할 수 있는 함수로 만들어주는 것이 좋다.&#x20;



이번엔 배열 고차 함수 예제를 살펴보자.&#x20;

보통 자바스크립트 입문자들이 아래와 같은 형식으로작성한다. ~~나..도 별반 다르지 않은 것 같은데~~

```javascript
function getSomeValue(params) {
    let tempVal = ''; // 이 임시 변수는 반복문과 조건문을 넘나들며 계속 변경된다. 
    
    for (let index = 0; index < array.length; index++) {
          temp += array[index];
          temp += array[index];
          temp += array[index];
          temp += array[index];   
    }
    
    if (temp ??) {
          tempVal = ??;
    } else if (temp ??) {
          temp += ??
    } else {
          temp += ??
    }
    
    return temp;
}
```

**따라서 우리는 어떻게 작성해야하냐?**&#x20;

**임시 변수를 사용하지 않는 방법을 많이 고민해야한다..! 💪**

### **결론**

* **임시 변수를 제거하자!**
  * 명령형으로 가득한 로직이기 때문에
  * 디버깅이 힘들다..
  * 추가적인 코드를 작성하려는 유혹이 생긴다..
* **해결 방법 😊**
  * **함수 나누기**
  * **바로 반환하기**
  * **고차 함수(`map`, `filter`, `reduce` ...)**
  * **선언형 프로그래밍**

## 호이스팅

* 호이스팅이란?&#x20;
  * 간단히 말해서 선언과 할당이 분리된 것을 말한다.&#x20;
  * 언제? 런타임(프로그램이 동작할 때)에
  * 변수 호이스팅
    * `var`로 선언한 변수가 초기화가 제대로 되어 있지 않을 때 `undefined`로 최상단에 끌어올려줄 수 있는 것을 말한다.&#x20;
    * `let`과 `const`를 쓰면 이런 현상이 잘 없음(TDZ)
  * 함수 호이스팅
    * 함수 표현식은 호이스팅이 일어나지 않는다.&#x20;
    * 그러나, 함수 선언식은 호이스팅이 발생한다!

다음 예제를 보자.

```javascript
var sum;

console.log(typeof sum); // function

function sum() {
    return 1 + 2;
}
```

`sum` 변수에 함수가 덮어쓰여져서(함수 호이스팅)`sum`의 타입을 찍어보면 `function`으로 나온다.

이러한 호이스팅은 프로그래밍에 안좋은 영향을 준다.&#x20;

**따라서, 함수를 만들 때 const를 사용한 함수 표현식을 사용하는 것이 좋다.**

```javascript
console.log(sum()); // ReferenceError: Cannot access 'sum' before initialization

const sum = function sum() {
    return 1 + 2;
}
```

`const`를 사용한 덕분에 선언하기 전에 접근하지 말라는 에러가 난다.

### 결론

* 호이스팅은 런타임 시에 바로 선언을 최상단으로 끌려올려주는 것
* 문제
  * 코드를 작성할 때 예측하지 못한 실행 결과가 노출된다.&#x20;
* **해결 방법 😊**
  * `var`를 지양한다. => `const`를 지향하자!
  * 함수 표현식을 사용한다.
