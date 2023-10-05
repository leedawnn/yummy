# Section 1.  ES6에서의 순회와 이터러블:이터레이터 프로토콜

## 기존과 달라진 ES6에서의 리스트 순회

### ES5에서의 방식

* for i++

```javascript
const list = [1, 2, 3];

for (var i = 0; i < list.length; i++) {
    console.log(list[i]);
}
```

### ES6에서의 방식

* for of

```javascript
for (const a of list) {
    console.log(a);
}
```

## 이터러블/이터레이터 프로토콜이 뭘까?

### 이터러블(iterable)

* **이터러블**은 반복 가능한 객체를 의미한다. 즉, 배열, 문자열,  Map, Set 등이 이터러블의 예시이다.
* **이터러블**은 이터레이터를 리턴하는 \[Symbol.iterator]\()를 가진 값이다.

### 이터레이터(iterator)

* ES6에서 도입된 **이터레이터**는 iterable한 데이터 구조를 순회하는 방법을 제공하는 객체이다.&#x20;
* **이터레이터**는 `next`라는 메서드를 가지고 있는데, 이를 호출하면 `{ value, done }` 객체를 리턴한다.
* `value` 프로퍼티는 현재 위치의 요소 값을 나타내고, `done` 프로퍼티는 모든 요소를 순회하였는지의 여부를 나타낸다.
* Array, Set, Map에서  `Symbol.iterator`를 확인해보면 구현되어 있는 메서드가 존재한다.

### 이터러블/이터레이터 프로토콜

* 이터러블을 for...of, 전개 연산자 등과 함께 동작하도록 한 규약

## 사용자 정의 이터러블

```javascript
const iterable = {
    [Symbol.iterator]() {
        let i = 3;
        return {
            next() {
                return i == 0 ? { done: true } : { value: i--, done: false };
            }
        }
    }
}

let iterator = iterable[Symbol.iterator]();
console.log(iterator.next()); // 요소 끝까지 계속 찍을 수 있음


for (const a of iterable) console.log(a);

const arr2 = [1, 2, 3];
for (const a of arr2) console.log(a);
```

이터러블에 `Symbol.iterator`가 구현되어 있기 때문에, for ... of문으로 순회를 할 수 있다. 그리고 `Symbol.iterator`가 실행될 때, value와 done이 포함된 객체를 리턴한다.&#x20;

이터레이터가 자기 자신을 반환하는 `Symbol.iterator`를 가질 때, _well-formed iterable_ 또는 _well-formed iterator_라고 부른다.&#x20;

## 전개 연산자

```javascript
const a = [1, 2];
console.log([...a, ...[3, 4]]);
```

전개 연산자도 이터러블 프로토콜을 따르는 값들을 펼칠 수 있다.

### 그래서, 이터레이터와 이터러블 프로토콜은 어떤 상황에서 유용할까?

* **비동기 데이터 처리** : 이터레이터는 비동기 작업을 순차적으로 처리하는 데 유용하다. 예를 들어, 여러 개의 비동기 API 요청을 순차적으로 처리해야 하는 경우에 Promise와 함께 이터레이터를 사용할 수 있다.&#x20;
* **사용자 정의 데이터 구조** : 직접 정의한 데이터 구조가 있다면, 그것을 `for...of`문 등으로 순회할 수 있도록 하려면 해당 객체에 이터러블 프로토콜을 구현해야한다.

