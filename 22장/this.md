22.1 this 키워드

객체: 프로퍼티(상태)와 메서드(동작)을 하나의 논리적인 단위로 묶은 복합적인 자료구조

객체는 메서드를 통해 프로퍼티를 변경할 수 있어야 하는데, 자신이 속한 객체의 프로퍼티를 참조하려면 그 객체를 가리키는 식별자를 참조할 수 있어야 한다.

객체 리터럴 방식으로 생성한 객체

```js
const circle = {
// circle 변수에 할당되기 직전 이미 객체 리터럴의 평가가 완료되어 객체가 생성됨
// 따라서 메서드 내부에서 circle 식별자를 참조할 수 있지만, 바람직하지 않다.
radius: 5,
getDiameter() {
return 2 * circle.radius;
}
};

console.log(circle.getDiameter());

getDiameter(). 메서드 내부에서 자신이 속한 객체를 재귀적으로 참조하고 있다.

2. 생성자 함수 방식으로 인스턴스를 생성

function Circle(radius) {
// 생성자 함수를 정의하는 시점에는 인스턴스 생성 전이므로 가리키는 식별자를 알 수 없음
????.radius = radius;
}

Circle.prototype.getDiameter = function () {
// 가리키는 식별자를 알 수 없음
return 2 * ????.radius;
}
```

// 생성자 함수로 인스턴스를 정의하기 위해서는 생성자 함수를 먼저 정의해야 한다.
const circle = new Circle(5);

this는 자신이 속한 객체/자신이 생성할 인스턴스를 가리키는 자기 참조 변수다. this를 통해 자신이 속한 객체와 생성할 인스턴스의 프로퍼티나 메서드를 참조할 수 있다.

- 자바스크립트 엔진에 의해 암묵적으로 생성

- 코드 어디서든 참조할 수 있다.

- 함수를 호출하면 arguments 객체와 this가 암묵적으로 함수 내부에 전달된다.

- this 바인딩은 함수 호출 방식에 의해 동적으로 결정된다. (식별자처럼)

- 객체 리터럴의 메서드 내부에서 this 는 메서드를 호출한 객체를 가리킨다. (속해있는객체)

- 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.

- 자바/C++ 등의 클래스 기반 언어는 this가 클래스가 생성하는 인스턴스를 가리키지만, 자바스크립트의 this 는 호출되는 방식에 따라 동적으로 결정된다. strict mode 또한 this 바인딩에 영향을 준다.

this는 객체의 프로퍼티/메서드를 참조하기 위한 자기 참조 변수이므로 생성자 함수 내부나 객체의 메서드 내부에서만 의미가 있다.

22.2 함수 호출 방식과 this 바인딩
렉시컬 스코프는 함수 정의가 평가되어 함수 객체가 생성되는 시점에 상위 스코프를 결정하지만, this 바인딩은 함수 호출 시점에 결정된다.

함수 호출 방법

1. 일반 함수 호출

2. 메서드 호출

3. 생성자 함수 호출

4. Function.prototype.apply/call/bind 메서드의 의한 간접 호출

   22.2.1 일반 함수 호출
   전역함수, 중첩함수를 일반함수로 호출하면 함수 내부의 this에는 전역 객체가 바인딩된다. (window)

그러나 this는 자기 참조 변수이므로 객체를 생성하지 않는 일반 함수에서는 의미가 없다.

strict mode에서는 undefined가 바인딩됨

```js
function foo() {
  'use strict';

  console.log(this); // undefined
  function bar() {
    console.log(this); // undefined
  }
  bar();
}
foo();
```

메서드 내부에서 정의한 중첩 함수도 일반 함수로 호출되면 중첩 함수 내부의 this에는 window가 바인딩됨

```js
var value = 1; // var 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티

const obj = {

value: 100,
foo() {
this; // {value: 100, foo: f}
this.value;

// 메서드 내부의 중첩 함수
function bar(){
this; // window
this.value; //1
}

bar();

// 콜백 함수가 일반 함수로 호출되며 콜백 함수 내부의 this에도 전역 객체가 바인딩 됨
setTimeout(function () {
this; //window
this.value; //1
}
}
};

obj.foo();
```

일반 함수로 호출된 모든 함수(중첩함수, 콜백함수 포함) 내부의 this에는 전역 객체가 바인딩 된다.

메서드 내부의 중첩 함수나 콜백함수의 this 바인딩을 메서드의 this 바인딩과 일치시키기 위해서?

1. that 사용

2. Function.prototype.apply

3. Function.prototype.call

4. Function.prototype.bind

5. 화살표 함수 사용

   22.2.2 메서드 호출

```js
const person = {
  name: 'Lee',
  getName() {
    // 메서드 내부의 this는 메서드를 호출한 객체에 바인딩
    return this.name;
  },
};

// 메서드 getName을 호출한 객체는 person
console.log(person.getName());
```

메서드는 객체에 포함된 것이 아니라 독립적으로 존재하는 별도의 객체다.

다른 객체의 프로퍼티에 할당하는 것으로 다른 객체의 메서드가 될 수도 있고 일반 변수에 할당하여 일반 함수로 호출될 수도 있다.

```js
const anotherPerson = {
  name: 'Kim',
};

// getName 메서드를 anotherPerson 객체의 메서드로 할당
anotherPerson.getName = person.getName;

console.log(anotherPerson.getName()); // kim

// getName 메서드를 변수에 할당
const getName = person.getName;

// getName 메서드를 일반 함수로 호출
console.log(getName()); // '' (window.name)
```

this에 바인딩될 객체는 호출 시점에 결정된다.

프로토타입 메서드 내부에서 사용된 this 도 해당 메서드를 호출한 객체에 바인딩 된다.

22.2.3 생성자 함수 호출
생성자 함수 내부의 this에는 생성자 함수가 생성할 인스턴스가 바인딩된다.

- new 연산자와 함께 생성자 함수를 호출하면 생성자 함수로 동작

- new 연산자를 사용하지 않으면 일반 함수로 동작 (undefined)

  22.2.4 Function.prototype.apply/call/bind 메서드에 의한 간접 호출
  apply, call, bind는 Function.prototype의 메서드로 모든 함수가 상속받아 사용할 수 있음

apply, call의 경우 arguments 객체와 같은 유사 배열 객체에 배열 메서드를 사용하느 경우에 이용한다.

```js
function getThisBinding() {
  return this;
}

const thisArg = { a: 1 };

console.log(getThisBinding()); //window

// 호출할 함수의 인수를 배열로 묶어 전달한다.
console.log(getThisBinding.apply(thisArg, [1, 2, 3]));

// 호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달한다.
console.log(getThisBinding.call(thisArg, 1, 2, 3));

// bind 메서드는 첫 번째 인수로 전달한 thisArg로 this 바인딩이 교체된다
// getThisBinding 함수를 새롭게 생성해 반환

console.log(getThisBinding.bind(thisArg)); // getThisBinding

// bind 메서드는 함수를 호출하지는 않으므로 명시적으로 호출해야 한다
console.log(getThisBinding.bind(thisArg)()); // {a: 1}
```

bind 메서드는 메서드의 this와 메서드 내부의 중첩 함수 또는 콜백 함수의 this가 불일치하는 문제를 해결할 때 사용

일반함수호출 : 전역 객체

메서드 호출 : 메서드를 호출한 객체

생성자 함수 호출 : 생성자 함수가 생성할 인스턴스

prototype 메서드에 의한 간접 호출 : 메서드에 첫번째 인수로 전달한 객체
