# Section 0.  함수형 자바스크립트 기본기

### 평가와 일급

#### 평가

코드가 계산되어 값을 만드는 것

#### 일급

```typescript
const a = 10; // 변수에 담을 수 있다.
const add10 = a => a + 10; // 함수의 인자로 사용될 수 있다.
```

* 값으로 다룰 수 있다.
* 변수에 담을 수 있다.
* 함수의 인자로 사용될 수 있다.
* 함수의 결과로 사용될 수 있다.

#### 일급 함수

```typescript
const f1 = () => () => 1; // 함수의 결과값으로 함수를 리턴할 수 있다.
console.log(f1()); // () => 1

const f2 = f1();
console.log(f2());
```

* 함수가 값으로 다뤄질 수 있다.
* 조합성과 추상화의 도구

#### 고차 함수

* 함수를 값으로 다루는 함수
* 고차 함수는 크게 2가지로 나눌 수 있다.&#x20;
  1. 함수를 인자로 받아서 실행하는 함수
  2. 함수를 만들어 리턴하는 함수 (클로저를 만들어 리턴하는 함수)

```typescript
// 1. 함수를 인자로 받아서 실행하는 함수
const apply1 = f => f(1);
const add2 = a => a + 2;
console.log(apply1(add2)); // 3

const times = (f, n) => {
    let i = -1;
    while (++i < n) f(i);
}

times(console.log, 3);
times(a => console.log(a + 10), 3);
```

```typescript
// 2. 함수를 만들어 리턴하는 함수 
const addMaker = a => b => a + b;
const add10 = addMaker(10);

console.log(add10); // b => a + b;
console.log(add10(10)); // 20
```
